---
layout: post
title: A Dude Who Thinks from The Prespective of Inkscape
---

<div class="message">
This blog is an inkscape note, I will talk about my inkscape experience to laser cutting last week. which will cover the understanding of “object to path” and “stroke to path”. And an answer to the fantastic long-run trouble about the missing lxml when using extensions.
</div>

### 1. Understanding "Object To Path"

#### 1.1 Probelm 1: 

After Marcel told me about [boxes.py](https://www.festi.info/boxes.py/) to generate the initial box files and apply modification on it. It seemed easy. We brought it to the laser cutter. It doesn’t work. orz. Paul helped to change the parameters for a great while. It still doesn’t work. orz

#### 1.2 Quick Answer: 

Applying <em>“Object to Path”</em>

#### 1.3 Detailed Answer: 

When I was standing there, I already knew that I messed up the ideas of an Object and a Path in inkscape. The requirement of “being understanding” is not only thinking from another human, such a beginning level, isn’t it. It seems like thinking from the perspective of a program is what all the mammals need to reach. lol

![placeholder](/image/2018-05-30-object.png)

Now, this is what inkscape looks at an object, it is a rectangle (with a very round corner though). two nodes (right up corner, left down corner) and one sliding bar (rad of the corner) define the object, I can change them to make a different shape rectangle, but i can’t go beyond a doomed rectangle. This is an Object.

save it as <em>.svg</em> file. this is what I get in the file:

{% highlight txt %}
<rect
 style=”opacity:0.67099998;fill:#ff6200;fill-opacity:1;fill-rule:nonzero;stroke:#ffe000;stroke-width:0.69999999;stroke-miterlimit:10;stroke-dasharray:none;stroke-opacity:1"
 id=”rect5971"
 width=”20.788691"
 height=”19.938244"
 x=”18.993303"
 y=”112.16964"
 ry=”9.9691219" 
/>
{% endhighlight %}

No wonder that I failed, I gave this to a laser cutter and expected it to go along the vector which was not there. I didn’t stand a chance.

Now applying <em>“Object to Path”</em>

![placeholder](/image/2018-05-30-path.png)

more nodes appear, suddenly we can change it into any shape we want. Not necessary to be a rectangle. The way inkscape looks at it changed.

![placeholder](/image/2018-05-30-changed-path.png)

save the path as <em>.svg</em> file. this is what I get, a path indicates the laser cutter to go along:

{% highlight txt %}
<path
 style=”opacity:0.67099998;fill:#ff6200;fill-opacity:1;fill-rule:nonzero;stroke:#ffe000;stroke-width:0.69999999;stroke-miterlimit:10;stroke-dasharray:none;stroke-opacity:1"
 d=”m 28.962425,112.16964 h 0.850447 c 5.522893,0 9.969122,4.44623 9.969122,9.96912 0,5.5229 -4.446229,9.96912 -9.969122,9.96912 h -0.850447 c -5.522893,0 -9.969122,-4.44622 -9.969122,-9.96912 0,-5.52289 4.446229,-9.96912 9.969122,-9.96912 z”
 id=”rect5971" 
/>
{% endhighlight %}

while I thought there would be no more trouble, I got another failure.

### 2. Understanding "Stroke To Path"

#### 2.1 Problem 2: 

Finally the laser cutter started to work, but it cut multiple times at the same path.

#### 2.2 Quick Answer: 

Being careful with <em>“Stroke to Path”</em>

#### 2.3 Detailed Answer: 

When I saw the way the laser cutter moved, it made me think that duplicated paths share the same places so it went over and over again. I made great great effort to try to get rid of the redundant lines, combined nodes. All didn’t work. I started to think maybe they were not overlapped. they were different paths just very close. I made a great close-up. That was really the reason. It came from when a stroke was set with a thickness, and after I applying “stroke to path”, paths along the edges of the original stroke were created.

before <em>“Stroke to Path”</em>, our path is like a single closed line (see the red thin line)

![placeholder](/image/2018-05-30-single-path.png)

after <em>“Stroke to Path”</em>, our path became the edges of the original stroke, they are two closed lines.

![placeholder](/image/2018-05-30-double-path.png)

That is why the laser cutter went through the same (but actually not exactly) path multiple times.

### 3. Extensions TroubleShooting:

#### 3.1 Problem 3:

When I tried to use Customized Extensions. I kept get missing lxml module error.

#### 3.2 Quick Answer: 

Check your python path

#### 3.3 Detailed Answer: 
This is such a pain. Because import lxml works just fine. I had no clue why it came along.

[All I found](http://www.inkscapeforum.com/viewtopic.php?t=12581) is saying that it was a long-term bug.

I took it for granted for days as an inkscape bug. I can do nothing about it. Stop being understanding. Until the day I became A Dude Who Thinks from The Prespective of Inkscape. Yeappy, pay attention when you have the same problem, especially when you are using virtual environment.

when I work on tensorflow, I realize that I am running the python of anaconda. All the library I installed goes to anaconda’s python. What if inkscape is told to use the default native python, of course it will complain, there is no lxml library there. I have to tell inkscape to use that anaconda’s python.

goto your <em>${HOME}/.config/inkscape/preferences.xml</em>

find the group tag with

{% highlight html %}
id=”extensions”
{% endhighlight %}

add one line:

{% highlight html %}
python-interpreter=”${HOME}/miniconda2/bin/python”
{% endhighlight %}

the problem is suddenly fixed.

### 4. Other small Notes 

when using inkscape for lasercutting:

- in inkscape, set width of stroke as <em>“0.1mm”</em>, in corelDraw, set as “Hairline”
two way to engrave, 1) set different colors, one for cutting, one for engrave, and setting cut way as “combine”, because engraving goes like bitmap. 2) make two runs, set less power for engrave part, then it will not able to go through.
- material and parameters: [https://hci.rwth-aachen.de/lasercutter](http://hci.rwth-aachen.de/lasercutter)
- lasercutter can cut paper
- inkscape extensions having <em>.py</em> and <em>.inx</em> to say where to put it in the menu. They all goes to <em>${HOME}/.congif/inkscape/extensions<em>, and restart the program. customized font folder in my machine /usr/share/fonts
- making a physical dot on the material along the laser calibration red pot. just in case when the job has to be interrupted. we still have a chance to continue. when the material is not standard size, set the canvas in program as real size to save material. And if still not confident, making power as 0 to see if every thing is in the area or use VisiCut to preview.
- in inkscape, the display unit <em>“mm”</em> is set at <em>File -> Document</em> Properties, select in object mode can change object size.
- one node can only connect to two other nodes. so don’t try to make three connects to a node.
- perfect fitting distance doesn’t make a tight-enough assembly, we used glue. tightness needs offset <em>(e.g. 0.1mm)</em>.
- when load .svg to blender as preview, path-combine the nodes. and making fill. Inter-section Filling option is not obvious, it is two icons on the right up corner of Fill setting.

![placeholder](/image/2018-05-30-inter-section-filling.png)

[END]
