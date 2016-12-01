---
published: true
title: Technical Support 
layout: post
author: H. Zhang
category: technical 
tags: [Ubuntu, Mac OS]
comments: true 
---

---

----

### 1. SSH installation errors ###

[Solution](http://askubuntu.com/questions/546983/ssh-installation-errors){:target="_blank"}

The problem is
- openssh-server : Depends: openssh-client (= 1:6.6p1-2ubuntu1) but 1:6.6p1-2ubuntu2 is installed.

		Try running: `sudo aptitude install openssh-client=1:6.6p1-2ubuntu1`

		This reverts to this version

		Then I did this: `sudo apt-get install openssh-server`

		Then the ssh localhost worked!!

----

### 2. Configure ssh service on Ubuntu ###

1. Install package: `sudo apt-get install openssh-server` and `sudo apt-get install openssh-clien`, the `openssh-client` is already installed by the system;
2. Start sshserver: `sudo service ssh start` and confirm `ps -e | grep ssh`;
3. Restart sshserver: `sudo /etc/init.d/ssh restart`;


<!--more-->


<!--嵌入 video 
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
