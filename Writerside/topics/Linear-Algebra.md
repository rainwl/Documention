# Linear Algebra

## Vector

### Dot Product

...

### Cross Product (vector product)

![](Cross_product.gif)

The magnitude of the cross product equals the area of a parallelogram with the
vectors for sides.

In particular, the magnitude of the product of two perpendicular
vectors is the product of their lengths. The units of the cross-product are
the product of the units of each vector. If two vectors are parallel or are
anti-parallel (that is, they are linearly dependent), or if either one
has zero length, then their cross product is zero.

```tex
\begin{align}
Cross\cdot Product:\\
\mathbf{a} \times \mathbf{b} &=\begin{Vmatrix}a\end{Vmatrix}\begin{Vmatrix}b\end{Vmatrix}\sin \theta \mathbf{n} \\
P\times Q&=< P_{y}Q_{z}-P_{z}Q_{y},P_{z}Q_{x}-P_{x}Q_{z},P_{x}Q_{y}-P_{y}Q_{z}> \\

Pseudo\cdot determinant:\\
P\times Q&=\begin{vmatrix}i  & j & k\\P_{x} & P_{y}  & P_{z} \\Q_{x} & Q_{y}  & Q_{z}\end{vmatrix}\\
\\
i&=< 1,0,0>\\
j&=< 1,0,0>\\
k&=< 1,0,0>\\
\end{align}
```

The outer product `PXQ` can also represent a linear change in vector `Q` based on vector `P`.
Linear transformation refers to the process of converting one vector to another vector by matrix multiplication.
In three dimensions, any linear transformation can be represented by a 3x3 matrix.

```tex
P\times Q =\begin{bmatrix}0  & -P_{z} &P_{y} \\P_{z} & 0 & -P_{x} \\-P_{y} &P_{x}  &0\end{bmatrix}\begin{bmatrix}Q_{x} \\Q_{y} \\Q_{z}\end{bmatrix}
```

## Matrix

<seealso>
    <category ref="wiki">
        <a href="https://en.wikipedia.org/wiki/Cross_product">Cross_product</a>
        <a href="https://en.wikipedia.org/wiki/Laplace_expansion">Laplace expansion</a>
    </category>
</seealso>