### 前言
原本想說用「斜率場」來畫電力線，但光靠斜率場實在太不精確，也無法對應實際的數學意義，因此決定根據公式來撰寫電場模擬。
大部分數學原理及程式碼都是參考網路上的資源。
參考資源多樣，也包含電磁學課本，恕不逐一放上。
數學(物理)原理:http://163.28.10.78/content/vocation/control/tp_nh/ee/tp_nh/7/3.htm


```python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.patches import Circle
```

## 下列方法所使用公式為 $ \vec{E} = k{\frac {Q} {r^3}}\vec{r}$


```python
def E(q, r0, x, y):
    """Return the electric field vector E=(Ex,Ey) due to charge q at r0."""
    den = np.hypot(x - r0[0], y - r0[1]) ** 3
    return q * (x - r0[0]) / den, q * (y - r0[1]) / den

```

* np.hypot 即是 $ \sqrt {(x^2 + y^2)} $


```python
# Grid of x, y points
nx, ny = 64, 64
x = np.linspace(-3, 3, nx)
y = np.linspace(-3, 3, ny)
X, Y = np.meshgrid(x, y)
```

## 決定電荷數量
設定np數量以決定模電荷子數量


```python
nq = 2 # the number of charges
charges = []
for i in range(nq):
    if i % 2 == 0:
        q = 1
    else:
        q = -1
    charges.append((q, (np.cos(2 * np.pi * i / nq), np.sin(2 * np.pi * i / nq))))
```

## Electric field vector, E=(Ex, Ey), as separate components


```python
# Electric field vector, E=(Ex, Ey), as separate components
Ex, Ey = np.zeros((ny, nx)), np.zeros((ny, nx))
for charge in charges:
    ex, ey = E(*charge, x=X, y=Y)
    Ex += ex
    Ey += ey
    
fig = plt.figure()
ax = fig.add_subplot(111)
```


![png](output_9_0.png)


## Plot the streamlines with an appropriate colormap and arrow style


```python
# Plot the streamlines with an appropriate colormap and arrow style
color = 2 * np.log(np.hypot(Ex, Ey))
ax.streamplot(x, y, Ex, Ey, color=color, linewidth=1, cmap=plt.cm.inferno,
              density=2, arrowstyle='->', arrowsize=1.5)
```




    <matplotlib.streamplot.StreamplotSet at 0x20cc72cf5e0>



## Add filled circles for the charges themselves


```python
# Add filled circles for the charges themselves
charge_colors = {True: '#aa0000', False: '#0000aa'}
for q, pos in charges:
    ax.add_artist(Circle(pos, 0.05, color=charge_colors[q > 0]))

ax.set_xlabel('$x$')
ax.set_ylabel('$y$')
ax.set_xlim(-2, 2)
ax.set_ylim(-2, 2)
ax.set_aspect('equal')

plt.show()
```

### 完整程式


```python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.patches import Circle

def E(q, r0, x, y):
    """Return the electric field vector E=(Ex,Ey) due to charge q at r0."""
    den = np.hypot(x - r0[0], y - r0[1]) ** 3
    return q * (x - r0[0]) / den, q * (y - r0[1]) / den

# Grid of x, y points
nx, ny = 64, 64
x = np.linspace(-3, 3, nx)
y = np.linspace(-3, 3, ny)
X, Y = np.meshgrid(x, y)

nq = 2 # the number of charges
charges = []
for i in range(nq):
    if i % 2 == 0:
        q = 1
    else:
        q = -1
    charges.append((q, (np.cos(2 * np.pi * i / nq), np.sin(2 * np.pi * i / nq))))
    
# Electric field vector, E=(Ex, Ey), as separate components
Ex, Ey = np.zeros((ny, nx)), np.zeros((ny, nx))
for charge in charges:
    ex, ey = E(*charge, x=X, y=Y)
    Ex += ex
    Ey += ey
    
fig = plt.figure()
ax = fig.add_subplot(111)

# Plot the streamlines with an appropriate colormap and arrow style
color = 2 * np.log(np.hypot(Ex, Ey))
ax.streamplot(x, y, Ex, Ey, color=color, linewidth=1, cmap=plt.cm.inferno,
              density=2, arrowstyle='->', arrowsize=1.5)

# Add filled circles for the charges themselves
charge_colors = {True: '#aa0000', False: '#0000aa'}
for q, pos in charges:
    ax.add_artist(Circle(pos, 0.05, color=charge_colors[q > 0]))

ax.set_xlabel('$x$')
ax.set_ylabel('$y$')
ax.set_xlim(-2, 2)
ax.set_ylim(-2, 2)
ax.set_aspect('equal')
plt.show()
```


![png](output_15_0.png)


## 下列為  $ \vec {E} = - \nabla {V} $


```python
def U(q, r0, x, y):
    """Return the electric field vector E=(Ex,Ey) due to charge q at r0."""
    den = np.hypot(x - r0[0], y - r0[1])
    return q / den


nq = 2
charges = []
for i in range(nq):
    if i % 2 == 0:
        q = 1
    else:
        q = -1
    charges.append((q, (np.cos(2 * np.pi * i / nq), np.sin(2 * np.pi * i / nq))))
    
U_array = np.zeros((nx, ny))    #set empty to '0'(or something)
for charge in charges:
    delta_U = U(*charge, x=X, y=Y)
    U_array += delta_U


Ey, Ex = np.gradient(-U_array)

fig = plt.figure()
ax = fig.add_subplot(111)

# Plot the streamlines with an appropriate colormap and arrow style
color = 2 * np.log(np.hypot(Ex, Ey))
ax.streamplot(x, y, Ex, Ey, color=color, linewidth=1, cmap=plt.cm.inferno,
              density=2, arrowstyle='->', arrowsize=1.5)

# Add filled circles for the charges themselves
charge_colors = {True: '#aa0000', False: '#0000aa'}
for q, pos in charges:
    ax.add_artist(Circle(pos, 0.05, color=charge_colors[q > 0]))

ax.set_xlabel('$x$')
ax.set_ylabel('$y$')
ax.set_xlim(-2, 2)
ax.set_ylim(-2, 2)
ax.set_aspect('equal')
ax.set_title('E=-grad(U)')
plt.show()

```


![png](output_17_0.png)

