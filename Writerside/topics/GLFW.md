# GLFW

## Configuration

## Window

## Triangle

Terminology
: Vertex Array Object `VAO`
: Vertex Buffer Object `VBO`
: Element Buffer Object `EBO`
: Index Buffer Object `IBO`

Graphics Pipeline
: **input** `3d coordinates`
: **output** `2d colorful pixels`
: **flow
** `vertex data[]`->`vertex shader`->`shape assemlby`->`geometry shader`->`rasterization`->`fragment shader`->`tests and blending`

Primitive
: `GL_POINTS`
: `GL_TRIANGLES`
: `GL_LINE_STRIP`

Graphics Pipeline Flow
: **Vertex Shader** 3d coordinates -> new 3d coordinates
: **Primitive Assembly** from vertex to primitive
: **Geometry Shader** from primitive to create new shape
: **Rasterization stage** map primitive to pixel,generate fragment for `fragment shader` and do clipping
: **Fragment Shader** compute the final color for each pixel
: **Alpha Test and Blending** do depth and stencil test and do blend

Normalized Device Coordinates NDC
: three axis all between `[-1,1]`

if we have some data about this like a triangle:

```C++
float vertices[] = {
    -0.5f, -0.5f, 0.0f,
     0.5f, -0.5f, 0.0f,
     0.0f,  0.5f, 0.0f
};
```

We send it as `input` to first stage of graphics pipeline **Vertex Shader**.
It will create memory on GPU to store our vertex data,and we need to configure OpenGL to explain this memory,and specify
how to send to GPU,then,**Vertex Shader** will handle the vertex in memory.

We manage this memory by **Vertex Buffer Object** ,it will store lots of vertices in GPU memory.

```C++
unsigned int VBO;
glGenBuffers(1, &VBO);
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
  
glDeleteBuffers(1, &VBO);
```

### Vertex Shader and Fragment Shader

```C++
#version 330 core
layout (location = 0) in vec3 aPos;

void main()
{
    gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
}
```

{collapsible="true" collapsed-title="vertex shader"}

```C++
#version 330 core
out vec4 FragColor;

void main()
{
    FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);
} 
```

{collapsible="true" collapsed-title="fragment shader"}

**Create a shader object**

```C++
// vertex shader
  const unsigned int vertex_shader = glCreateShader(GL_VERTEX_SHADER);
  glShaderSource(vertex_shader, 1, &vertexShaderSource, nullptr);
  glCompileShader(vertex_shader);
  check_success(vertex_shader);
  
  // fragment shader
  const unsigned int fragment_shader = glCreateShader(GL_FRAGMENT_SHADER);
  glShaderSource(fragment_shader, 1, &fragmentShaderSource, nullptr);
  glCompileShader(fragment_shader);
  check_success(fragment_shader);
  
  // link shaders
  const unsigned int shader_program = glCreateProgram();
  glAttachShader(shader_program, vertex_shader);
  glAttachShader(shader_program, fragment_shader);
  glLinkProgram(shader_program);
  check_success(shader_program);
 
  glDeleteShader(vertex_shader);
  glDeleteShader(fragment_shader);
```
{collapsible="true" collapsed-title="shader"}
