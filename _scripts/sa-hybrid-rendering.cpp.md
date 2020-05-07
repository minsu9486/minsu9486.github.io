---
title: "HybridRendering.cpp"
permalink: /scripts/sa-hybrid-rendering.cpp/
layout: splash
excerpt: "3D Spatial Algorithms"
---

***

~~~c++
/* Start Header -------------------------------------------------------
Purpose: - This is a sandbox for showing loaded objects and others.
         - It's implemented a hybrid rendering pipeline that employs a Deferred
           Rendering method for the objects of interest, coupled with the usual
           Forward Rendering for auxiliary rendering.
         - (2/23/2020) recursive_directory_iterator is added for loading
           many objects in a folder (Power Plant)
         - (4/16/2020) Adaptive Octree
Language: ISO C++17 Standard (/std:c++17)
Platform: 16.0, x64, Window10
Project: Spatial Algorithms - Hybrid Rendering
Author: Minsu Kang
Creation date: 1/13/2020
End Header --------------------------------------------------------*/

  .
  .
  .

bool SceneHybridRendering::Init_GBuffer_Configration()
{
  glGenFramebuffers(1, &gBufferFBO);
  glBindFramebuffer(GL_FRAMEBUFFER, gBufferFBO);

  // position buffer (GL_RGB)
  glGenTextures(1, &gPosition);
  glBindTexture(GL_TEXTURE_2D, gPosition);
  glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB16F, this->_windowWidth, this->_windowHeight, 0, GL_RGB, GL_FLOAT, NULL);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
  glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, gPosition, 0);

  // normal buffer (GL_RGB)
  glGenTextures(1, &gNormal);
  glBindTexture(GL_TEXTURE_2D, gNormal);
  glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB16F, this->_windowWidth, this->_windowHeight, 0, GL_RGB, GL_FLOAT, NULL);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
  glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT1, GL_TEXTURE_2D, gNormal, 0);

  // color (albedo) and specular buffer (GL_RGBA)
  glGenTextures(1, &gAlbedoSpec);
  glBindTexture(GL_TEXTURE_2D, gAlbedoSpec);
  glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA, this->_windowWidth, this->_windowHeight, 0, GL_RGBA, GL_UNSIGNED_BYTE, NULL);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
  glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT2, GL_TEXTURE_2D, gAlbedoSpec, 0);

  // tell OpenGL which color attachments we'll use (of this framebuffer) for rendering 
  unsigned int attachments[3] = { GL_COLOR_ATTACHMENT0, GL_COLOR_ATTACHMENT1, GL_COLOR_ATTACHMENT2 };
  glDrawBuffers(3, attachments);

  // create and attach depth buffer (renderbuffer)
  unsigned int rboDepth;
  glGenRenderbuffers(1, &rboDepth);
  glBindRenderbuffer(GL_RENDERBUFFER, rboDepth);
  glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH_COMPONENT, this->_windowWidth, this->_windowHeight);
  glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_ATTACHMENT, GL_RENDERBUFFER, rboDepth);

  // finally check if framebuffer is complete
  if (glCheckFramebufferStatus(GL_FRAMEBUFFER) != GL_FRAMEBUFFER_COMPLETE)
  {
    printf("gbuffer program failed to compile");
    return false;
  }

  // restore default FBO
  glBindFramebuffer(GL_FRAMEBUFFER, 0);

  // shader configuration
  // --------------------
  shaderFSQBuffer->use();
  shaderFSQBuffer->setInt("gPosition", 0);
  shaderFSQBuffer->setInt("gNormal", 1);
  shaderFSQBuffer->setInt("gAlbedoSpec", 2);

  return true;
}

int SceneHybridRendering::Render(const float dt)
{
  .
  .
  .

  // "gBufferFBO" is user defined FBO
  Render_Deferred_Objects(gBufferFBO);

  // Copy gBufferFBO's depth buffer into default OpenGL depth buffer (0)
  if (bCopyDepthInfo) // Toggle this capability with input from user (GUI).
    Copy_Depth_Info(gBufferFBO, 0);

  // This will draw all dubug data.
  Render_Debug_Objects();

  return 0;
}

// Implement the Deferred Shading pipeline for rendering the OBJ files using FBO’s
void SceneHybridRendering::Render_Deferred_Objects(const GLint fbo)
{
  /////////////////////////////////////////////////////////////////////////////
  // First pass: Generate the G-buffer using multiple render targets.
  glBindFramebuffer(GL_FRAMEBUFFER, gBufferFBO);
  glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
  glm::mat4 projection = glm::perspective(glm::radians(m_pCamera->Zoom), (float)this->_windowWidth / (float)this->_windowHeight, 0.1f, 100.0f);
  glm::mat4 view = m_pCamera->GetViewMatrix();
  glm::mat4 model = glm::mat4(1.0f);

  shaderGBuffer->use();
  shaderGBuffer->setMat4("projection", projection);
  shaderGBuffer->setMat4("view", view);

  for (auto pObj : pObjectList)
  {
    model = glm::mat4(1.0f);
    model = glm::translate(model, pObj->position);
    // model = glm::rotate(model, angleOfCenterObjRotation, glm::vec3(0.0f, 1.0f, 0.0f));
    model = glm::scale(model, pObj->scale);
    shaderGBuffer->setMat4("model", model);
    shaderGBuffer->setVec3("color", pObj->color);
    shaderGBuffer->setInt("renderTarget", renderTarget);
    pObj->Draw(shaderGBuffer);
  }

  glBindFramebuffer(GL_FRAMEBUFFER, 0);

  /////////////////////////////////////////////////////////////////////////////
  // Second Pass: Use the render targets from Pass 1 as input textures to the
  //              Lighting Pass. The Vertex Shader will be a straight pass -
  //              through to render a Full Screen Quad(FSQ).
  glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
  shaderFSQBuffer->use();
  glActiveTexture(GL_TEXTURE0);
  glBindTexture(GL_TEXTURE_2D, gPosition);
  glActiveTexture(GL_TEXTURE1);
  glBindTexture(GL_TEXTURE_2D, gNormal);
  glActiveTexture(GL_TEXTURE2);
  glBindTexture(GL_TEXTURE_2D, gAlbedoSpec);

  // Uniform Blocks (as before) to pass light information.
  int lightIndex = 0;
  const float linear = 0.7;
  const float quadratic = 1.8;
  for (auto pLight : pLightList)
  {
    if (pLight->isActivated == false)
      continue;

    shaderFSQBuffer->setVec3("lights[" + std::to_string(lightIndex) + "].Position", pLight->position);
    shaderFSQBuffer->setVec3("lights[" + std::to_string(lightIndex) + "].Color", pLight->color);

    // update attenuation parameters
    shaderFSQBuffer->setFloat("lights[" + std::to_string(lightIndex) + "].Linear", linear);
    shaderFSQBuffer->setFloat("lights[" + std::to_string(lightIndex) + "].Quadratic", quadratic);

    ++lightIndex;
  }

  // send general light data
  shaderFSQBuffer->setInt("currLightsNum", numOfSpheres);
  shaderFSQBuffer->setVec3("viewPos", m_pCamera->Position);
  shaderFSQBuffer->setInt("renderTarget", renderTarget);

  glPolygonMode(GL_FRONT_AND_BACK, GL_FILL); // FSQ should be always randered!
  RenderFSQ(); // FSQBuffer shader on a quad (FSQ)
}

.
.
.
~~~