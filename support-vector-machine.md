# SVM
**Search for decision margin which maximize distance between points and hyperplane**

![](https://github.com/werdnakof/Advanced-Machine-Learning-Notes/blob/master/images/svm.png?raw=true)

To increase the likelihood of linear-separability, high-dimensional mapping is often used

$$
x = (x_1, x_2, ... , x_p) \rightarrow 
\phi(x) = (\phi_1(x), \phi_2(x), ... , \phi_m(x))
$$ where $$ m >> p $$

Finding the maximum margin hyper-plane is time consuming in
“primal” form if $m$ is large, we can work in the “dual” space of patterns, then we only need to compute dot products

$$
\vec\phi(x_i) \cdot  \vec\phi(x_j) = 
{\sum\limits_{k = 1}^m  \phi_k(x_i)\ \phi_k(x_j)}
 $$

## Kernel Trick

Choose a **positive semi-definite** kernel function $K(x, y)$ such that:
$$
K(x_i, x_y) = \vec\phi(x_i) \cdot \vec\phi(x_j)
$$

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTc3NTAxMzczM119
-->