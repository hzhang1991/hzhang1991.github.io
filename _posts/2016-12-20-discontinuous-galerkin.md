---
published: true 
title: Note of Discontinuous Galerkin method 
layout: post
author: H. Zhang
category: note 
tags: [Discontinuous Galerkin]
comments: true 
---

---

### Li Fenguyan. 2011. A tutorial on Discontinuous Galerkin method ###
Ref: [pdf](https://www.birs.ca/workshops/2011/11w5086/files/FengyanLi_TutorialOnDGM.pdf)

<!--more-->

#### RKDG for 1d Scalar Conservation Laws ####

\begin{align}  
	u_t + f(u)_x = 0, \quad 0 < x < 1, t>0 \\\
	u(x, 0) = u_0(x), \quad 0 < x < 1.
\end{align}  

#### An *inconsistent* DG method ####

Consider the heat equation
\begin{equation}
	\left\{
		\begin{aligned}
			u_t = u_xx, \quad 0 < x < 1, t > 0, \\\ 
			u(x, 0) = u_0(x), \quad 0 < x < 1.
		\end{aligned}
	\right.
\end{equation}

A generalization to the heat equation $u_t - u_{xx} = u_t + (-u_x)_x = 0$:

look for $u_h \in V_h$ such that $\forall v \in V_h$,
\begin{align}
	\int_\Omega u_{h,t} v dx + \int_\Omega u_{h,x} v_x dx - (\hat{u_{h,x}})_{j+1/2} v^-_{j+1/2} + (\hat{u_{h,x}})_{j-1/2} v^+_{j-1/2} = 0,
\end{align}
and the central flux is a natural choice:
$\hat{u_{h,x}}_{j+1/2} = \{u_{h,x}\}_{j+1/2}$.


### Shu Chiwang. Discontinuous Galerkin Methods: General Approach and Stability ###
Ref: [pdf](https://www3.nd.edu/~zxu2/acms60790S15/DG-general-approach.pdf)





Quarteron's book



<!-- markdown template
## H2 title ##

> blaock quotes
> hquotes
>
> > nested block quote
>
> back to first quote

> ## title ##
>
> 1. list
>
> code
>
> return 

* red
+ blue
- green

1. hi
2. hello

* sdsfsf
	tab**
	hi
	
	isdfsf

* hii
	> black quote
	> inside

1910\. this is not a list

## separation ##
***
******


------

[link](http:// "title")
see my [About](/about/) page for details.

This is an [example][id] reference.

[id]: http:// "title"






-->
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
