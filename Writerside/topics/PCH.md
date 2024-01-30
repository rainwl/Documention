# PCH

## Step

- Create two files named `PCH.h` and `PCH.cpp`
- Right click project and choose `Properties`
- In `Configuration Properties`->`C/C++`->`Precompiled Headers`->`Precompiled Header` : **Use (/Yu)**
- In `Configuration Properties`->`C/C++`->`Precompiled Headers`->`Precompiled Header File` : **PCH.h**
- And right click the `PCH.cpp` and choose `Properties`
- In `Configuration Properties`->`C/C++`->`Precompiled Headers`->`Precompiled Header` : **Create (/Yc)**
- In `Configuration Properties`->`C/C++`->`Precompiled Headers`->`Precompiled Header File` : **PCH.h**
- Then add `#include "PCH.h"` to all source files (.cpp) under the project

> In Visual Studio,`Tools`->`Options`->`Projects and Solutions`->`VC++ Project Settings`->`Build Timing` : **Yes**
> {style="note"}

> If the PCH.h file is shared between two projects, pay attention to the configuration of each project, such as linker or include, to ensure that PCH.h is clicked in each project without error.
> {style = "warning"}

## PCH.h example



```C++
#pragma once

#include <core/aabbtree.h>
#include <core/cloth.h>
#include <core/core.h>
#include <core/extrude.h>
#include <core/mat22.h>
#include <core/mat33.h>
#include <core/mat44.h>
#include <core/maths.h>
#include <core/matnn.h>
#include <core/mesh.h>
#include <core/perlin.h>
#include <core/pfm.h>
#include <core/platform.h>
#include <core/png.h>
#include <core/point3.h>
#include <core/quat.h>
#include <core/sdf.h>
#include <core/tga.h>
#include <core/types.h>
#include <core/vec2.h>
#include <core/vec3.h>
#include <core/vec4.h>
#include <core/voxelize.h>

#include <algorithm>
#include <iostream>
#include <iomanip>
#include <chrono>
#include <string>
#include <map>
#include <ctime>
#include <cstdlib>

#include "../external/SDL2-2.0.4/include/SDL.h"

#include "../include/NvFlex.h"
#include "../include/NvFlexExt.h"
#include "../include/NvFlexDevice.h"

#include <demo/scenes/mesh_collision/bvh.h>
#include <demo/scenes/mesh_collision/dmMeshCollision.h>
#include <demo/scenes/mesh_collision/triangleTriangleIntersection.h>
```