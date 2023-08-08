---
title: Plant3D-Custom Scripts Via Python
date: 2023-02-12 19:35:30
tags:
cover: https://s3.bmp.ovh/imgs/2023/01/21/b9b5ab8e8b18eac5.jpg
---

#  Plant3D Custom Scripts Via Python

[typro 破解 - Search (bing.com)](https://cn.bing.com/search?q=typro+破解&cvid=78dcc678017b4780822d731b01adb03f&aqs=edge..69i57j0l8.6402j0j1&pglt=163&FORM=ANNTA1&DAF0=1&PC=CNNDDB&ntref=1)

[Custom Python Scripts for AutoCAD Plant 3D Part 1 - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2015/06/custom-python-scripts-for-autocad-plant-3d-part-1.html)

[Custom Python Scripts for AutoCAD Plant 3D Part 2 - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2015/06/custom-python-scripts-for-autocad-plant-3d-part-2.html)

[Custom Python Scripts for AutoCAD Plant 3D Part 3 - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2015/06/custom-python-scripts-for-autocad-plant-3d-part-3.html)

[Custom Python Scripts for AutoCAD Plant 3D Part 4 - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2015/07/custom-python-scripts-for-autocad-plant-3d-part-4.html)

[Custom Python Scripts for AutoCAD Plant 3D Part 5 - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2016/01/custom-python-scripts-for-autocad-plant-3d-part-5.html)

```python
#(arxload "PnP3dACPAdapter")
#(testacpscript "EXPJOINT1")
```



##  Code1--HALF CYLINDER

![](https://s3.bmp.ovh/imgs/2023/02/13/5d8178316ef32dfc.png)

```python
def HALF_CYLINDER(s,radiusOuter=30.15, heightCyli=40.0, radiusInner=0.0, **kw):
    halfCyli=CYLINDER(s, R=radiusOuter, H=heightCyli, O=radiusInner)
    sTemp=BOX(s,L=2.0*radiusOuter,H=radiusOuter,W=heightCyli).translate((-radiusOuter/2.0,0,heightCyli/2.0))
    halfCyli.subtractFrom(sTemp).rotateY(-90).translate((heightCyli/2.0,0,0.0))
    sTemp.erase()
    return halfCyli
```

##  Code2--Bend Filter

![](https://s3.bmp.ovh/imgs/2023/02/13/689b5af037ae8ab7.png)

```python
def BEND_FILTER(s,bendRadiusOuter=16,bendWidth=30,bendRadiusInner=8):
    sTempBOX=BOX(s,L=bendRadiusOuter,H=bendWidth,W=bendRadiusOuter).translate((0,bendRadiusOuter/2.0,bendRadiusOuter/2.0))
    bendFilter=HALF_CYLINDER(s,radiusOuter=bendRadiusOuter, heightCyli=bendWidth, radiusInner=bendRadiusInner).subtractFrom(sTempBOX)
    return bendFilter
```

##  Code3--U CLAMP

![](https://s3.bmp.ovh/imgs/2023/02/13/d7a4822f6793c836.png)

```python
def CLAMP(s,clampRadiusOuter=75.15, heightClamp=40.0, widthClamp=40.0,thicknessClamp=5.0,lengthClamp=210.0,boltHolesDistance=184.0, boltHoleDia=18.0,bendRadius=5,**kw):
    clampTemp=BOX(s, L=2.0*clampRadiusOuter, H=widthClamp, W=heightClamp).translate((0.0,0.0,-heightClamp/2.0)).uniteWith(BOX(s,L=lengthClamp,W=thicknessClamp,H=widthClamp).translate((0.0,0,thicknessClamp/2.0-heightClamp)))
    clampTemp.uniteWith(BOX(s,L=lengthClamp,H=widthClamp,W=thicknessClamp).translate((0,0,-heightClamp+thicknessClamp/2.0)))
    clampTemp.subtractFrom(BOX(s,L=2.0*(clampRadiusOuter-thicknessClamp),H=widthClamp,W=heightClamp).translate((0,0,-heightClamp/2.0)))
    clampTemp.uniteWith(HALF_CYLINDER(s,radiusOuter=clampRadiusOuter,heightCyli=widthClamp,radiusInner=clampRadiusOuter-thicknessClamp))

    # # Bending
    cutBox=BOX(s, L=2.0*(clampRadiusOuter+bendRadius), H=widthClamp, W=bendRadius+thicknessClamp).translate((0.0,0.0,-heightClamp)).translate((0.0,0.0,(bendRadius+thicknessClamp)/2.0))
    clampTemp.subtractFrom(cutBox)

    bend1=BEND_FILTER(s,bendRadiusOuter=bendRadius+thicknessClamp,bendWidth=widthClamp,bendRadiusInner=bendRadius).rotateX(90).translate((0,clampRadiusOuter+bendRadius,-heightClamp+bendRadius+thicknessClamp))
    bend2=BEND_FILTER(s,bendRadiusOuter=bendRadius+thicknessClamp,bendWidth=widthClamp,bendRadiusInner=bendRadius).rotateX(180).translate((0,-clampRadiusOuter-bendRadius,-heightClamp+bendRadius+thicknessClamp))
    clampTemp.uniteWith(bend1)
    clampTemp.uniteWith(bend2)

    # BoltHole
    if boltHoleDia>0:
        boltHole=CYLINDER(s, R=boltHoleDia/2.0, H=thicknessClamp, O=0.0).translate((0,-boltHolesDistance/2.0,-heightClamp))
        boltHole=boltHole.uniteWith(CYLINDER(s, R=boltHoleDia/2.0, H=thicknessClamp, O=0.0).translate((0,boltHolesDistance/2.0,-heightClamp)))
        clampTemp.subtractFrom(boltHole)

    return clampTemp
```

##  Code4--Oblong Shape

![](https://s3.bmp.ovh/imgs/2023/02/14/e82d8dce53ee7b20.png)

The origin point is at the center of entity.

``` python
def OBLONG_SHAPE(s,dia=24.0, len=72.0, height=6.0, **kw):
    cYli01=CYLINDER(s, R=dia/2.0, H=height, O=0.0).translate((0,-(len-dia)/2.0,-height/2.0))
    cenBox=BOX(s,L=len-dia,H=dia,W=height)
    cYli02=CYLINDER(s, R=dia/2.0, H=height, O=0.0).translate((0,(len-dia)/2.0,-height/2.0))
    cYli01.uniteWith(cenBox)
    cYli01.uniteWith(cYli02)
    return cYli01
```





##  Summary

```py
# -*- coding: utf-8 -*-
#______________U型管道、法兰管卡______________________
from varmain.primitiv import *
from varmain.custom import *

"""
(arxload "PnP3dACPAdapter")
(testacpscript "U_CLAMP")
"""
def HALF_CYLINDER(s,radiusOuter=30.15, heightCyli=40.0, radiusInner=0.0, **kw):
    halfCyli=CYLINDER(s, R=radiusOuter, H=heightCyli, O=radiusInner)
    sTemp=BOX(s,L=2.0*radiusOuter,H=radiusOuter,W=heightCyli).translate((-radiusOuter/2.0,0,heightCyli/2.0))
    halfCyli.subtractFrom(sTemp).rotateY(-90).translate((heightCyli/2.0,0,0.0))
    sTemp.erase()
    return halfCyli

def BEND_FILTER(s,bendRadiusOuter=16,bendWidth=30,bendRadiusInner=8):
    sTempBOX=BOX(s,L=bendRadiusOuter,H=bendWidth,W=bendRadiusOuter).translate((0,bendRadiusOuter/2.0,bendRadiusOuter/2.0))
    bendFilter=HALF_CYLINDER(s,radiusOuter=bendRadiusOuter, heightCyli=bendWidth, radiusInner=bendRadiusInner).subtractFrom(sTempBOX)
    return bendFilter


def CLAMP(s,clampRadiusOuter=75.15, heightClamp=40.0, widthClamp=40.0,thicknessClamp=5.0,lengthClamp=210.0,boltHolesDistance=184.0, boltHoleDia=18.0,bendRadius=5,**kw):
    clampTemp=BOX(s, L=2.0*clampRadiusOuter, H=widthClamp, W=heightClamp).translate((0.0,0.0,-heightClamp/2.0)).uniteWith(BOX(s,L=lengthClamp,W=thicknessClamp,H=widthClamp).translate((0.0,0,thicknessClamp/2.0-heightClamp)))
    clampTemp.uniteWith(BOX(s,L=lengthClamp,H=widthClamp,W=thicknessClamp).translate((0,0,-heightClamp+thicknessClamp/2.0)))
    clampTemp.subtractFrom(BOX(s,L=2.0*(clampRadiusOuter-thicknessClamp),H=widthClamp,W=heightClamp).translate((0,0,-heightClamp/2.0)))
    clampTemp.uniteWith(HALF_CYLINDER(s,radiusOuter=clampRadiusOuter,heightCyli=widthClamp,radiusInner=clampRadiusOuter-thicknessClamp))

    # # Bending
    cutBox=BOX(s, L=2.0*(clampRadiusOuter+bendRadius), H=widthClamp, W=bendRadius+thicknessClamp).translate((0.0,0.0,-heightClamp)).translate((0.0,0.0,(bendRadius+thicknessClamp)/2.0))
    clampTemp.subtractFrom(cutBox)

    bend1=BEND_FILTER(s,bendRadiusOuter=bendRadius+thicknessClamp,bendWidth=widthClamp,bendRadiusInner=bendRadius).rotateX(90).translate((0,clampRadiusOuter+bendRadius,-heightClamp+bendRadius+thicknessClamp))
    bend2=BEND_FILTER(s,bendRadiusOuter=bendRadius+thicknessClamp,bendWidth=widthClamp,bendRadiusInner=bendRadius).rotateX(180).translate((0,-clampRadiusOuter-bendRadius,-heightClamp+bendRadius+thicknessClamp))
    clampTemp.uniteWith(bend1)
    clampTemp.uniteWith(bend2)

    # BoltHole
    if boltHoleDia>0:
        boltHole=CYLINDER(s, R=boltHoleDia/2.0, H=thicknessClamp, O=0.0).translate((0,-boltHolesDistance/2.0,-heightClamp))
        boltHole=boltHole.uniteWith(CYLINDER(s, R=boltHoleDia/2.0, H=thicknessClamp, O=0.0).translate((0,boltHolesDistance/2.0,-heightClamp)))
        clampTemp.subtractFrom(boltHole)

    return clampTemp

def OBLONG_SHAPE(s,dia=24.0, len=72.0, height=6.0, **kw):
    cYli01=CYLINDER(s, R=dia/2.0, H=height, O=0.0).translate((0,-(len-dia)/2.0,-height/2.0))
    cenBox=BOX(s,L=len-dia,H=dia,W=height)
    cYli02=CYLINDER(s, R=dia/2.0, H=height, O=0.0).translate((0,(len-dia)/2.0,-height/2.0))
    cYli01.uniteWith(cenBox)
    cYli01.uniteWith(cYli02)
    return cYli01

@activate(Group="Support", Ports="1", TooltipShort="U CLAMP FOR PIPE OR FLANGE",LengthUnit="mm")  
@group("MainDimensions")
@param(D=LENGTH, TooltipLong="Pipe/Flange Dimension")
@param(R=LENGTH, TooltipLong=u"折弯板折弯内径")
@param(t=LENGTH, TooltipLong=u"折弯板厚度")
@param(H=LENGTH, TooltipLong=u"折弯板折弯中心距折弯板底部高度距离")
@param(A=LENGTH, TooltipLong=u"螺栓孔中心距离")
@param(B=LENGTH, TooltipLong=u"保冷木托管卡长度")
@param(C=LENGTH, TooltipLong=u"保冷木托/管卡宽度")
@param(d=LENGTH, TooltipLong=u"螺栓孔直径")
@param(r=LENGTH, TooltipLong=u"管卡折弯板折弯半径")
@param(ShowHole=INT, TooltipLong=u"是否显示开孔示意")

def U_CLAMP(s, D=273, R=139.5, t=8.0, H=133.5, A=330.0, B=400.0, C=50.0, d=18.0,r=8.0,ShowHole=1,**kw):
# def U_CLAMP2(s, **kw):
    # D=273
    # R=139.5
    # t=8
    # H=133.5
    # A=330
    # B=400.0
    # C=50.0
    # d=18.0
    # r=8.0
    # ShowHole=1  
    
    #管卡
    sClamp=CLAMP(s,clampRadiusOuter=R+t, heightClamp=H, widthClamp=C,thicknessClamp=t,lengthClamp=B,boltHolesDistance=A, boltHoleDia=d,bendRadius=r)
    sPad=BOX(s,L=D/3.0*2.0,H=C,W=R).translate((0,0,-R/2.0))
    sPad.subtractFrom(CYLINDER(s,R=D/2.0,H=C).translate((0,0,-C/2.0)).rotateY(90))

    # show the bolt holes on the steel structure
    if ShowHole==1:
        boltHole=OBLONG_SHAPE(s,dia=d, len=2.0*d, height=6.0).translate((0,A/2.0,0))
        boltHole.uniteWith(OBLONG_SHAPE(s,dia=d, len=2.0*d, height=6.0).translate((0,-A/2.0,0)))
        steelPlate=BOX(s,L=B,H=C,W=6).subtractFrom(boltHole).translate((0,0,-R-3))

    # Points
    disBottomToCenter=R

    s.setLinearDimension('disBottomToCenter',(0,0,-1.0),(0,0,-R)) #底部基准点
    s.setLinearDimension('disBottomToCenter',(0,1.0,0),(0,A/2.0,-R)) #左侧螺栓孔中心点1
    s.setLinearDimension('disBottomToCenter',(0,1.0,0),(0,B/2.0,-R)) #左侧折弯板中心点1
    s.setLinearDimension('disBottomToCenter',(0,-1.0,0),(0,-A/2.0,-R)) #右侧螺栓孔中心点1
    s.setLinearDimension('disBottomToCenter',(0,-1.0,0),(0,-B/2.0,-R)) #右侧折弯板中心点1

    # Base Point
    s.setPoint((0.0, 0.0, 0.0), (-1.0, 0.0, 0.0))
```

