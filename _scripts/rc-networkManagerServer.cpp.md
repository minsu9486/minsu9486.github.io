---
title: "NetworkManagerServer.cpp"
permalink: /scripts/rc-networkManagerServer.cpp/
layout: splash
excerpt: "Robo Cat, Multiplayer Game"
---

***

~~~cpp
.
.
.

void NetworkManagerServer::ProcessPacket( ClientProxyPtr inClientProxy, InputMemoryBitStream& inInputStream )
{
	//remember we got a packet so we know not to disconnect for a bit
	inClientProxy->UpdateLastPacketTime();

	uint32_t	packetType;
	inInputStream.Read( packetType );
	switch( packetType )
	{
	case kHelloCC:
		//need to resend welcome. to be extra safe we should check the name is the one we expect from this address,
		//otherwise something weird is going on...
		SendWelcomePacket( inClientProxy );
		break;
	case kInputCC:
		if( inClientProxy->GetDeliveryNotificationManager().ReadAndProcessState( inInputStream ) )
		{
			HandleInputPacket( inClientProxy, inInputStream );
		}
		break;
	default:
		LOG( "Unknown packet type received from %s", inClientProxy->GetSocketAddress().ToString().c_str() );
		break;
	}
}

void NetworkManagerServer::HandlePacketFromNewClient( InputMemoryBitStream& inInputStream, const SocketAddress& inFromAddress )
{
	//read the beginning- is it a hello?
	uint32_t	packetType;
	inInputStream.Read( packetType );
	if(  packetType == kHelloCC )
	{
		string name; // username
		inInputStream.Read( name );

    string avatar;
    inInputStream.Read( avatar );

    string token;
    inInputStream.Read( token );

    // concatenate the values to build the message
    string message = name + avatar + mSharedSecret;

#ifdef _WIN32
#else
    DigestBuilder digestBuilder;
    string digest = digestBuilder.BuildDigest(message);

    if (token.compare(digest) != 0)
	{
      LOG("Token verification has been failed: %s", name.c_str());
	  return;
	}
#endif

		ClientProxyPtr newClientProxy = std::make_shared< ClientProxy >( inFromAddress, name, mNewPlayerId++ );
		mAddressToClientMap[ inFromAddress ] = newClientProxy;
		mPlayerIdToClientMap[ newClientProxy->GetPlayerId() ] = newClientProxy;
		
		//tell the server about this client, spawn a cat, etc...
		//if we had a generic message system, this would be a good use for it...
		//instead we'll just tell the server directly
		static_cast< Server* > ( Engine::sInstance.get() )->HandleNewClient( newClientProxy );

		//and welcome the client...
		SendWelcomePacket( newClientProxy );

		//and now init the replication manager with everything we know about!
		for( const auto& pair: mNetworkIdToGameObjectMap )
		{
			newClientProxy->GetReplicationManagerServer().ReplicateCreate( pair.first, pair.second->GetAllStateMask() );
		}
	}
	else
	{
		//bad incoming packet from unknown client- we're under attack!!
		LOG( "Bad incoming packet from unknown client at socket %s", inFromAddress.ToString().c_str() );
	}
}

void NetworkManagerServer::SendWelcomePacket( ClientProxyPtr inClientProxy )
{
	OutputMemoryBitStream welcomePacket; 

	welcomePacket.Write( kWelcomeCC );
	welcomePacket.Write( inClientProxy->GetPlayerId() );

	LOG( "Server Welcoming, new client '%s' as player %d", inClientProxy->GetName().c_str(), inClientProxy->GetPlayerId() );

	SendPacket( welcomePacket, inClientProxy->GetSocketAddress() );
}

.
.
.
~~~