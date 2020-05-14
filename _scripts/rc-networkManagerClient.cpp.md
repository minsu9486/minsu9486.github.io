---
title: "NetworkManagerClient.cpp"
permalink: /scripts/rc-networkManagerClient.cpp/
layout: splash
excerpt: "Robo Cat, Multiplayer Game"
---

***

~~~cpp
.
.
.

void NetworkManagerClient::ProcessPacket(InputMemoryBitStream& inInputStream, const SocketAddress& inFromAddress)
{
  uint32_t	packetType;
  inInputStream.Read(packetType);
  switch (packetType)
  {
  case kWelcomeCC:
    HandleWelcomePacket(inInputStream);
    break;
  case kStateCC:
    if (mDeliveryNotificationManager.ReadAndProcessState(inInputStream))
    {
      HandleStatePacket(inInputStream);
    }
    break;
  }
}

void NetworkManagerClient::SendOutgoingPackets()
{
  switch (mState)
  {
  case NCS_Login:
    UpdateLogin();
    break;
  case NCS_Connect:
    UpdateConnect();
    break;
  case NCS_SayingHello:
    UpdateSayingHello();
    break;
  case NCS_Welcomed:
    UpdateSendingInputPacket();
    break;
  }
}

void NetworkManagerClient::UpdateLogin()
{
  http::status_code currStatusCode;

  json::value postData, json_return;
  postData[L"username"] = json::value::string(utility::conversions::to_string_t(mUserData.username));
  postData[L"password"] = json::value::string(utility::conversions::to_string_t(mUserData.password));

  web::uri serverURI = utility::conversions::to_string_t(mServerLoginDestination);
  http_client client(serverURI);
  client.request(web::http::methods::POST, U("/api/v1/login"), postData)

    .then([&currStatusCode](const web::http::http_response& response) {
    currStatusCode = response.status_code();
    return response.extract_json();
      })

    .then([&json_return](const pplx::task<web::json::value>& task) {
        try {
          json_return = task.get();
        }
        catch (const web::http::http_exception& e) {
          LOG("ERROR, UpdateLogin: %s", e.what());
        }
      })

        .wait();

      if (currStatusCode == http::status_codes::OK)
      {
        // move to the next state, getting the game server destination
        mState = NCS_Connect;

        json::value jValStrSession = json_return.at(U("session"));
        json::value jValStrToken = json_return.at(U("token"));

        mUserData.session = utility::conversions::to_utf8string(jValStrSession.as_string());
        mUserData.token = utility::conversions::to_utf8string(jValStrToken.as_string());
      }
      else
      {
        // change the current state to the idle state
        mState = NCS_Uninitialized;
        LOG("RETURN FAILED, UpdateLogin: %hu", currStatusCode);
      }
}

void NetworkManagerClient::UpdateConnect()
{
  http::status_code currStatusCode;

  json::value postData, json_return;
  postData[L"session"] = json::value::string(utility::conversions::to_string_t(mUserData.session));
  postData[L"token"] = json::value::string(utility::conversions::to_string_t(mUserData.token));

  web::uri serverURI = utility::conversions::to_string_t(mServerLoginDestination);
  http_client client(serverURI);
  client.request(web::http::methods::POST, U("/api/v1/connect"), postData)

    .then([&currStatusCode](const web::http::http_response& response) {
    currStatusCode = response.status_code();
    return response.extract_json();
      })

    .then([&json_return](const pplx::task<web::json::value>& task) {
        try {
          json_return = task.get();
        }
        catch (const web::http::http_exception& e) {
          LOG("ERROR, UpdateConnect: %s", e.what());
        }
      })

        .wait();

      if (currStatusCode == http::status_codes::OK)
      {
        // move to the next state, connecting to the given game server
        mState = NCS_SayingHello;

        json::value jValStrID = json_return.at(U("id"));
        json::value jValStrAvatar = json_return.at(U("avatar"));
        json::value jValStrServer = json_return.at(U("server"));
        json::value jValStrtoken = json_return.at(U("token"));

        mUserData.id = utility::conversions::to_utf8string(jValStrID.as_string());
        mUserData.avatar = utility::conversions::to_utf8string(jValStrAvatar.as_string());
        mUserData.server = utility::conversions::to_utf8string(jValStrServer.as_string());
        mUserData.token = utility::conversions::to_utf8string(jValStrtoken.as_string());

        mServerAddress = *(SocketAddressFactory::CreateIPv4FromString(mUserData.server));

        LOG("RETURN, UpdateConnect: %s | %s | %s",
          utility::conversions::to_utf8string(jValStrID.as_string()).c_str(),
          utility::conversions::to_utf8string(jValStrAvatar.as_string()).c_str(),
          utility::conversions::to_utf8string(jValStrServer.as_string()).c_str()
        );
      }
      else
      {
        LOG("RETURN FAILED, UpdateConnect: %hu", currStatusCode);
      }
}

void NetworkManagerClient::SendHelloPacket()
{
  OutputMemoryBitStream helloPacket;

  helloPacket.Write(kHelloCC);
  helloPacket.Write(mUserData.username);
  helloPacket.Write(mUserData.avatar);
  helloPacket.Write(mUserData.token);

  SendPacket(helloPacket, mServerAddress);
}

.
.
.
~~~