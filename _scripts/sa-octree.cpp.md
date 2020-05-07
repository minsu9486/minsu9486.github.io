---
title: "Octree.cpp"
permalink: /scripts/sa-octree.cpp/
layout: splash
excerpt: "3D Spatial Algorithms"
---

***

~~~c++
/* Start Header -------------------------------------------------------
Purpose: Creating octrees in top-down fashion.
         This creates subtrees reclusively.
Language: ISO C++17 Standard (/std:c++17)
Platform: 16.0, x64, Window10
Project: Spatial Algorithms - Octree
Author: Minsu Kang
Creation date: 4/15/2020
End Header --------------------------------------------------------*/

Octree::Octree(std::list<Object*>& _pObjectList)
{
  m_MaxDepth = 0;
  targetTreeHeight = 0;
  vector<Vertex*> pointCloud;

  // Find max and min positions to init the first entire volume
  glm::vec3 maxPosition = glm::vec3(-std::numeric_limits<float>::max());
  glm::vec3 minPosition = glm::vec3(std::numeric_limits<float>::max());
  for (Object* pObj : _pObjectList)
  {
    maxPosition = glm::max(maxPosition, pObj->GetMaxPos());
    minPosition = glm::min(minPosition, pObj->GetMinPos());

    for (auto& mesh : pObj->pModel->meshes)
    {
      for(auto& vertex : mesh.vertices)
        pointCloud.push_back(&vertex);
    }
  }

  // to get the center pos and the half width
  glm::vec3 octreeCenterPos = (maxPosition + minPosition) / 2.f;
  float octreeHalfWidth = abs(maxPosition.x - octreeCenterPos.x);
  octreeHalfWidth = glm::max(octreeHalfWidth, abs(maxPosition.y - octreeCenterPos.y));
  octreeHalfWidth = glm::max(octreeHalfWidth, abs(maxPosition.z - octreeCenterPos.z));

  // Construct and fill in root of this subtree
  m_pOctree = new Octree();
  m_pOctree->center = octreeCenterPos;
  m_pOctree->halfWidth = octreeHalfWidth;
  m_pOctree->pBox = new BoxAABB(
    glm::vec3(octreeCenterPos + octreeHalfWidth),
    glm::vec3(octreeCenterPos - octreeHalfWidth));
  m_pOctree->pBox->centerPosition = octreeCenterPos;
  m_pOctree->currDepth = 0;

  // It's time to fill octree!!!
  InsertVertices(m_pOctree, pointCloud);
  pointCloud.clear();
}

void Octree::BuildChildNodes(Octree* pTree, const glm::vec3 center, const float halfWidth, const int currDepth)
{
  // construct the eight children of the subtree
  glm::vec3 offset;
  float step = halfWidth * 0.5f;
  pTree->pChild = new Octree[8]; // allocate

  for (int i = 0; i < 8; i++) {
    offset.x = ((i & 1) ? step : -step);
    offset.y = ((i & 2) ? step : -step);
    offset.z = ((i & 4) ? step : -step);

    const glm::vec3 childCenter = center + offset;
    const float childHalfWidth = step;

    // init data
    pTree->pChild[i].center = childCenter;
    pTree->pChild[i].halfWidth = childHalfWidth;
    pTree->pChild[i].pBox = new BoxAABB(childCenter + childHalfWidth, childCenter - childHalfWidth);
    pTree->pChild[i].pBox->centerPosition = center;
    pTree->pChild[i].pBox->color = glm::vec3(1.f);
    pTree->pChild[i].currDepth = currDepth + 1;
  }
}

.
.
.

void Octree::InsertVertices(Octree* pTree, vector<Vertex*> _pointCloud)
{
  // Terminating criteria should be 300 (or less) triangles in the current cell.
  if (_pointCloud.size() < 300)
    return;

  // When it cannot split its volume anymore
  if (pTree->halfWidth < glm::epsilon<float>())
  {
    printf("ERROR: Cannot split its volume anymore!!");
    return;
  }

  if(pTree->pChild == nullptr)
    BuildChildNodes(pTree, pTree->center, pTree->halfWidth, pTree->currDepth);

  vector<Vertex*> pointCloud[8];
  glm::vec3 colors[8];

  // Display octree cells with each �level� in different color
  for (int i = 0; i < 8; ++i)
  {
    // random color between 0.4 and 1.0
    colors[i].x = ((rand() % 100) / 200.0f) + 0.4f;
    colors[i].y = ((rand() % 100) / 200.0f) + 0.4f;
    colors[i].z = ((rand() % 100) / 200.0f) + 0.4f;
  }

  // Find the given vertex's child index among 0 - 7 sub nodes
  for(auto& pVertex : _pointCloud)
  {
    int index = -1;
    glm::vec3 delta = pVertex->Position - pTree->center;

    if (delta.x < 0)
    {
      if (delta.y < 0)
      {
        if (delta.z < 0)
          index = 0;
        else
          index = 4;
      }
      else
      {
        if (delta.z < 0)
          index = 2;
        else
          index = 6;
      }
    }
    else
    {
      if (delta.y < 0)
      {
        if (delta.z < 0)
          index = 1;
        else
          index = 5;
      }
      else
      {
        if (delta.z < 0)
          index = 3;
        else
          index = 7;
      }
    }

    pVertex->NodeColor = colors[index];
    pointCloud[index].push_back(pVertex);
  }

  for (int i = 0; i < 8; ++i)
  {
    pTree->pChild[i].pBox->color = colors[0];
    InsertVertices(&(pTree->pChild[i]), pointCloud[i]);
  }

  m_MaxDepth = glm::max(m_MaxDepth, pTree->currDepth + 1);
}

void Octree::Draw(Shader* pShader)
{
  DrawAux(pShader, m_pOctree, 0);
}

void Octree::DrawAux(Shader* pShader, Octree* pNode, int currentLevel)
{
  // show all nodes
  if (targetTreeHeight == -1)
  {
    // termination
    if (pNode->pChild == NULL)
      return;

    // children
    for (int i = 0; i < 8; ++i)
    {
      DrawAux(pShader, &(pNode->pChild[i]), currentLevel + 1);
      pNode->pChild[i].pBox->Draw(pShader);
    }
  }
  // show only the target node
  else
  {

    if (currentLevel == targetTreeHeight)
    {
      pNode->pBox->Draw(pShader);
      return;
    }

    if (pNode->pChild == NULL)
      return;

    // children
    for (int i = 0; i < 8; ++i)
      DrawAux(pShader, &(pNode->pChild[i]), currentLevel + 1);
  }
}
~~~