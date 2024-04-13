# Tensor Calculus

## 1-order Derivatives {collapsible="true"}

![gradient](gradient.png) {width="400"}

> Gradient is the steepest direction for increasing f. It's perpendicular to the iso surface.
> {style="note"}

### Partial derivative of function of a real variable

```tex
\begin{align}
 f(x)&\in R \\
 df  &=  \frac{\partial f}{\partial x} dx+\frac{\partial f}{\partial y} dy+\frac{\partial f}{\partial z} dz  =  \begin{bmatrix}\frac{\partial f}{\partial x}   \frac{\partial f}{\partial y}   \frac{\partial f}{\partial z}\end{bmatrix}\begin{bmatrix}dx \\dy \\dz\end{bmatrix}
\end{align}
```
### Gradient
```tex
\begin{align}
 \frac{\partial f}{\partial \mathbf{x} } &= \begin{bmatrix}\frac{\partial f}{\partial x}  & \frac{\partial f}{\partial y}  & \frac{\partial f}{\partial z}\end{bmatrix} \\
 \nabla f(x)&=\begin{bmatrix}\frac{\partial f}{\partial x}  \\\frac{\partial f}{\partial y} \\\frac{\partial f}{\partial z}\end{bmatrix} 
\end{align}
```
### Jacobi Matrix
```tex
\begin{align}
f(\mathbf{x} )&=\begin{bmatrix}f(\mathbf{x} )\\g(\mathbf{x} ) \\h(\mathbf{x} )\end{bmatrix}\in \mathbf{R} ^{3} \\
Jacobi: \\
\mathbf{J} (x)&=\frac{\partial \mathbf{f} }{\partial \mathbf{x} } =\begin{bmatrix}\frac{\partial f}{\partial x} & \frac{\partial f}{\partial y}  & \frac{\partial f}{\partial z} \\\frac{\partial g}{\partial x} & \frac{\partial g}{\partial y}  & \frac{\partial g}{\partial z} \\\frac{\partial h}{\partial x} & \frac{\partial h}{\partial y}  & \frac{\partial h}{\partial z} \end{bmatrix} \\
Divergence:\\
\nabla \cdot f&=\frac{\partial f}{\partial x} +\frac{\partial g}{\partial y} +\frac{\partial h}{\partial z} \\
Curl:\\
\nabla \times f&=\begin{bmatrix}\frac{\partial h}{\partial y} -\frac{\partial g}{\partial z} \\\frac{\partial f}{\partial z} -\frac{\partial h}{\partial x}  \\\frac{\partial g}{\partial x} -\frac{\partial f}{\partial y} \end{bmatrix}
\end{align}
```

## 2-order Derivatives {collapsible="true"}
### Hessian Laplacian
```tex
\begin{align}
f(\mathbf{x} )&\in R \\
Hessian:\\
H&=J(\nabla f(x))=\begin{bmatrix}\frac{\partial ^{2}f }{\partial x^{2} }   & \frac{\partial ^{2}f }{\partial x \partial y}  & \frac{\partial ^{2}f }{\partial x \partial z}\\\frac{\partial ^{2}f }{\partial x \partial y}   & \frac{\partial ^{2}f }{\partial y^{2} }  & \frac{\partial ^{2}f }{\partial y \partial z}\\\frac{\partial ^{2}f }{\partial x \partial z}   & \frac{\partial ^{2}f }{\partial y \partial z} & \frac{\partial ^{2}f }{\partial z^{2}}\end{bmatrix}\\
Laplacian:\\
\nabla \cdot \nabla f(x)&=\nabla ^{2}f(x)=\Delta f(x)=\frac{\partial ^{2}f}{\partial x^{2}}+ \frac{\partial ^{2}f}{\partial y^{2}}+\frac{\partial ^{2}f}{\partial z^{2}}
\end{align}
```

### Taylor Expansion

```tex
\begin{align}
f(\mathbf{x} )&\in R \\
f(x)&=f(x_{0})+\frac{\partial f(x_{0})}{\partial x} (x-x_{0})+\frac{1}{2}\frac{\partial f^{2}(x_{0})}{\partial x^{}2}(x-x_{0})^{2}+...\\
f(\mathbf{x} )&=f(\mathbf{x} _{0})+\frac{\partial f(\mathbf{x} _{0})}{\partial \mathbf{x} } (\mathbf{x} -\mathbf{x} _{0})+\frac{1}{2}(\mathbf{x} -\mathbf{x} _{0})^{T}\frac{\partial f^{2}(\mathbf{x} _{0})}{\partial \mathbf{x} ^{2}}(\mathbf{x} -\mathbf{x} _{0})^{2}+...\\
&=f(\mathbf{x} _{0})+\nabla f(\mathbf{x} _{0})\cdot (\mathbf{x} -\mathbf{x}_{0})+\frac{1}{2} (\mathbf{x} -\mathbf{x} _{0})^{T}H(\mathbf{x} -\mathbf{x} _{0})+...\\
s.p.d&\\
V^{T}AV&>0
\end{align}
```

## Vector 's norm 's derivative {collapsible="true"}

> The gradient of a vector indicates that the shortest side length of the vector can be changed, which is equivalent to
> normalizing the direction.
> {style="note"}

```tex
\begin{align}
\frac{\partial \begin{Vmatrix}x\end{Vmatrix}}{\partial x} &=\frac{x^{T}}{\begin{Vmatrix}x\end{Vmatrix}}\\
\frac{\partial \begin{Vmatrix}x\end{Vmatrix}}{\partial x} &=\frac{\partial (x^{T}x)^{1/2}}{\partial x} =\frac{1}{2} (x^{T}x)^{-1/2}\frac{\partial (x^{T}x)}{\partial x} = \frac{1}{2\begin{Vmatrix}x\end{Vmatrix}} 2x^{T}=\frac{x^{T}}{\begin{Vmatrix}x\end{Vmatrix}}\\
\frac{\partial (x^{T}x)}{\partial x} &=\frac{\partial (x^{2}+y^{2}+z^{2})}{\partial x} =\begin{bmatrix}2x & 2y & 2z\end{bmatrix} =2x^{T}
\end{align}
```

## Spring with 1 end stiffness {collapsible="true"}

![spring](Spring.png){width="400"}

```tex
\begin{align}
Energy:\\
E(x)&=\frac{k}{2} (\begin{Vmatrix}x\end{Vmatrix}-L)^{2}\\
Force:\\
f(x)&=-\nabla E(x)=-k(\begin{Vmatrix}x\end{Vmatrix}-L)(\frac{\partial \begin{Vmatrix}x\end{Vmatrix}}{\partial x} )^{T}=-k(\begin{Vmatrix}x\end{Vmatrix}-L)\frac{x}{\begin{Vmatrix}x\end{Vmatrix}}\\
Tangent Stiffness:\\
H(x)&=-\frac{\partial f(x)}{\partial x} =k\frac{xx^{T}}{\begin{Vmatrix}x\end{Vmatrix}^{2}}+k(\begin{Vmatrix}x\end{Vmatrix}-L) \frac{I}{\begin{Vmatrix}x\end{Vmatrix}} -k(\begin{Vmatrix}x\end{Vmatrix}-L)\frac{x}{\begin{Vmatrix}x\end{Vmatrix}^{2}} \frac{x^{T}}{\begin{Vmatrix}x\end{Vmatrix}}\\
&=k\frac{xx^{T}}{\begin{Vmatrix}
x
\end{Vmatrix}^{2}}+k(1-\frac{L}{\begin{Vmatrix}
x
\end{Vmatrix}} ) (I-\frac{xx^{T}}{\begin{Vmatrix}
x
\end{Vmatrix}^{2}} ) 
\end{align}
```

## Spring with 2 ends stiffness {collapsible="true"}

![](Spring2.png){width="400"}

```tex
\begin{align}
Energy:\\
E(x)&=\frac{k}{2} (\begin{Vmatrix} x_{01} \end{Vmatrix}-L)^{2}\\
Force:\\
f(x)&=-\nabla E(x)=\begin{bmatrix}
-\nabla _{0}E(x) \\
-\nabla _{1}E(x)
\end{bmatrix}=\begin{bmatrix}
 f_{e}\\
-f_{e}
\end{bmatrix}\\
f_{e}&=-k(\begin{Vmatrix}
x_{01}
\end{Vmatrix}-L)\frac{x_{01}}{\begin{Vmatrix}
x_{01}
\end{Vmatrix}}\\
Tangent  Stiffness:\\
H(x)&=\begin{bmatrix}
 \frac{\partial ^{2}E}{\partial x^{2}_{0}}  & \frac{\partial ^{2}E}{\partial x_{0}\partial x_{1}} \\
 \frac{\partial ^{2}E}{\partial x_{0}\partial x_{1}} & \frac{\partial ^{2}E}{\partial x^{2}_{1}}
\end{bmatrix}=\begin{bmatrix}
 H_{e} & -H_{e} \\
 -H_{e} & H_{e}
\end{bmatrix}\\
H_{e}&=k\frac{x_{01}x^{T}_{01}}{\begin{Vmatrix}
x_{01}
\end{Vmatrix}^{2}} +k(1-\frac{L}{\begin{Vmatrix}
x_{01}
\end{Vmatrix}} )(I-\frac{x_{01}x^{T}_{01}}{\begin{Vmatrix}
x_{01}
\end{Vmatrix}^{2}} )
\end{align}
```

<seealso>
    <category ref="wiki">
        <a href="https://en.wikipedia.org/wiki/Partial_derivative">Partial_derivative</a>
        <a href="https://en.wikipedia.org/wiki/Jacobian_matrix_and_determinant">Jacobian matrix and determinant</a>
        <a href="https://en.wikipedia.org/wiki/Hessian_matrix">Hessian_matrix</a>
        <a href="https://en.wikipedia.org/wiki/Chain_rule">Chain_rule</a>
    </category>
</seealso>