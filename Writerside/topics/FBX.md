# FBX

## obj
```C++
  bool LoadObjMesh(const char *path) {
    std::ifstream file(path);

    if (!file) return false;
    if (part_offsets.empty()) part_offsets.push_back(0);
    while (file) {
      constexpr uint32_t k_max_line_length = 1024;
      char buffer[k_max_line_length];
      file >> buffer;

      if (buffer[0] == 'v') {
        // positions
        float x, y, z;
        file >> x >> y >> z;

        positions.push_back(Vec3(-x, y, z));
      } else if (buffer[0] == 's' || buffer[0] == 'o') {
        // ignore smoothing groups, groups and objects
        char line_buf[256];
        file.getline(line_buf, 256);
      } else if (buffer[0] == 'g') {
        int part_index;
        file >> part_index;

        part_offsets.push_back(positions.size());
      } else if (buffer[0] == 'f') {
        //直接忽略
        char line_buf[256];
        file.getline(line_buf, 256);
      } else if (buffer[0] == '#') {
        // comment
        char line_buf[256];
        file.getline(line_buf, 256);
      }
    }
    file.close();
    

    auto x = static_cast<std::string>(path);
    
    std::ofstream out_file(x+".txt");
    if (!out_file) {
      return  false;
    }
    out_file << "Positions:\n";
    for (const auto& pos : positions) {
      out_file << pos.x << " " << pos.y << " " << pos.z << "\n";
    }

    out_file << "Part Offsets:\n";
    for (const auto& offset : part_offsets) {
      out_file << offset << "\n";
    }

    out_file.close();
    
    return true;
  }
```

## fbx

Import `assimp-vc143-mt.lib` and `dll` and include `assimp/`
```C++
#include <assimp/Importer.hpp>
#include <assimp/scene.h>
#include <assimp/postprocess.h>


 bool LoadMeshFromFbx(const char *path) {
    Assimp::Importer importer;
    const aiScene *scene = importer.ReadFile(path, aiProcess_Triangulate | aiProcess_JoinIdenticalVertices | aiProcess_SortByPType);
    if (!scene) {
      std::cerr << "Error: " << importer.GetErrorString() << std::endl;
      return false;
    }
    if (part_offsets.empty()) { part_offsets.push_back(0); }
    
    aiMatrix4x4 identity;
    ProcessNode(scene->mRootNode, scene, identity);
    
    importer.FreeScene();

    auto x = static_cast<std::string>(path);
    
    std::ofstream out_file(x+".txt");
    if (!out_file) {
      return  false;
    }
    out_file << "Positions:\n";
    for (const auto& pos : positions) {
      out_file << pos.x * 10 << " " << pos.y * 10 << " " << pos.z * 10 << "\n";
    }

    out_file << "Part Offsets:\n";
    for (const auto& offset : part_offsets) {
      out_file << offset << "\n";
    }

    out_file.close();
    
    return true;
  }
  
  void ProcessNode(const aiNode* node, const aiScene* scene, const aiMatrix4x4& parentTransform) {
    aiMatrix4x4 transform = parentTransform * node->mTransformation;

    for (unsigned int m = 0; m < node->mNumMeshes; ++m) {
      const aiMesh* mesh = scene->mMeshes[node->mMeshes[m]];
      for (unsigned int v = 0; v < mesh->mNumVertices; ++v) {
        aiVector3D ai_pos = mesh->mVertices[v];
        ai_pos = transform * ai_pos;
        positions.push_back(Vec3(ai_pos.x, ai_pos.y, ai_pos.z));
      }
      part_offsets.push_back(positions.size());
    }

    for (unsigned int c = 0; c < node->mNumChildren; ++c) {
      ProcessNode(node->mChildren[c], scene, transform);
    }
  }
```