---
published: false
title: FEniCS tutorial 
layout: post
author: H. Zhang
category: note 
tags: [FEniCS]
comments: true 
---

---

### Write an upwind scheme using DG and RT elements

```python
from dolfin import *
mesh = UnitSquareMesh(1,1)
RT = FunctionSpace(mesh, "RT", 1)
DG = FunctionSpace(mesh, "DG", 0)

# advection quantity
C = Function(DG)
C.interpolate(Expression("(x[0]>0.5)+1.0"))                                                             

n = FacetNormal(mesh)                                                                                   
un = dot(U,n)    
up = (un + abs(un))/2.0  
um = (un - abs(un))/2.0
Ct = TestFunction(DG)                                                                                   
flux = ((up*Ct*C)('+') + (up*Ct*C)('-') + (um*Ct)('+')*C('-') + (um*Ct)('-')*C('+'))*dS  

U.interpolate(Constant((0.1,0.0)))                                                                      
print assemble(flux).array()                                                                            

U.interpolate(Constant((-0.1,0.0)))                                                                     
print assemble(flux).array()  

```

The outward flux can be calculated as:
$$Q_{out} = (u \cdot \vec{n} + \| u \cdot \vel{n} \|) / 2$$


<!--more-->


<!--宓屽叆 video 
<iframe height=498 width=510 src="http://player.youku.com/embed/XMTY1MTI3NjMyNA==" frameborder=0 allowfullscreen></iframe>

<embed src="http://player.youku.com/player.php/Type/Folder/Fid/27690810/Ob/1/sid/XMTY1MTI3NjMyNA==/v.swf" quality="high" width="480" height="400" align="middle" allowScriptAccess="always" allowFullScreen="true" mode="transparent" type="application/x-shockwave-flash"></embed>

<video width="480" height="320" controls>
<source src="movie.mp4">
</video>
-->

<!-- insert audio
<audio src="http://sc.111ttt.com/up/mp3/314720/8F9F3E8438FE1581248E92B54A3C0AB5.mp3" controls="controls">
</audio>
-->

<!-- Insert pdf 
<iframe src="/pdf/mou.pdf" style="width:300px; height:100px;" frameborder="0"></iframe>
-->

<!-- insert pdf doc use google view
<iframe src="http://docs.google.com/gview?url=http://platinhom.github.io/pdf/mou.pdf&embedded=true" style="width:800px; height:1000px;" frameborder="0"></iframe>
-->
