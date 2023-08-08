---
title: Plant3d API
date: 2023-01-21 00:32:04
tags:
cover: https://s3.bmp.ovh/imgs/2023/01/21/b9b5ab8e8b18eac5.jpg
---

#  Plant3D API



## Getting started with AutoCAD P&ID/Plant 3D 2013 API

[AutoCAD P ID and Plant 3D Technologies | Autodesk Developer Network](https://www.autodesk.com/developer-network/platform-technologies/autocad-p-id-and-plant-3d?_ga=2.29442367.1607432708.1674231356-1836955287.1673691923)

[Getting started with AutoCAD PID/Plant 3D 2013 API - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2012/10/getting-started-with-autocad-pidplant-3d-2013-api.html)



[Plant3d .NET API DLL Dependency Map](https://adndevblog.typepad.com/autocad/2012/09/plant3d-net-api-dll-dependency-map.html)

[How to obtain a list of all Pipe Specs and Sizes in Plant3d using .NET C# - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2012/05/how-to-obtain-a-list-of-all-pipe-specs-and-sizes-in-plant3d-using-net-c.html)

[Reconnect all components in a drawing](https://forums.autodesk.com/t5/autocad-plant-3d-forum/reconnect-all-components-in-a-drawing/m-p/10887378)

[API, How to get P3D Thread connection object / properties by LineNumber?](https://forums.autodesk.com/t5/autocad-plant-3d-forum/api-how-to-get-p3d-thread-connection-object-properties-by/m-p/5827121)

[API how to assign the Norminal Size/Spec, Insulation Type/Thickness?](https://forums.autodesk.com/t5/autocad-plant-3d-forum/api-how-to-assign-the-norminal-size-spec-insulation-type/m-p/5816877)

[How to get all available properties, standard and custom through API](https://forums.autodesk.com/t5/autocad-plant-3d-forum/how-to-get-all-available-properties-standard-and-custom-through/m-p/10177701)

[API, How to get P3D object by LineNumber?](https://forums.autodesk.com/t5/autocad-plant-3d-forum/api-how-to-get-p3d-object-by-linenumber/m-p/5818840)

[DataLink API !](https://forums.autodesk.com/t5/autocad-plant-3d-forum/datalink-api/m-p/3054232)

[How to handle Actuator Mappings using the API?](https://forums.autodesk.com/t5/autocad-plant-3d-forum/how-to-handle-actuator-mappings-using-the-api/m-p/10525433)



[AutoCAD .NET 二次开发实例(6) winform交互界面（一） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/138579148)

[AutoCAD.NET 下二次开发启动WPF模态窗口_潜龙在渊的博客-CSDN博客_cad二次开发 wpf](https://blog.csdn.net/kdf123/article/details/98073128)

[CAD二次开发实例: 一些基于C#的CAD二次开发实例 (gitee.com)](https://gitee.com/yuzhaokai/CADExampleDemo)

[Create IsoSphereSymbol in Plant3d using ObjectARX](https://adndevblog.typepad.com/autocad/2012/09/create-isospheresymbol-in-plant3d-using-objectarx.html)

https://www.youtube.com/watch?v=ivpiNPMjVu8&list=PLH6JtcBCXj8h8nh61ZAspYQUSrAKFJnpF&index=2



##  CAD打开资源管理器对话框选择文件

http://www.theswamp.org/

~~~  c#
using System.Windows.Forms;

public string GetDialogToExcel()
{
    try
    {
        OpenFileDialog fileDialog = new OpenFileDialog();  //选择文件对话框 
        fileDialog.Multiselect = true;                     //可不可以同时选几个文件
        fileDialog.Title = "请选择包含图名图号的EXCEL文件";
        fileDialog.Filter = "Excel Office97-2003(*.xls)|*.xls|Excel Office2007及以上(*.xlsx)|*.xlsx";
        DialogResult result = fileDialog.ShowDialog();
        if (result == DialogResult.OK)
        {
            if (System.IO.Directory.Exists(fileDialog.FileName))    //验证路径是否为空或非法
            {
                return fileDialog.FileName;
            }
        }
    }
    catch(System.Exception ex)
    {
        Editor ed = Autodesk.AutoCAD.ApplicationServices.Application.DocumentManager.MdiActiveDocument.Editor;
        ed.WriteMessage("\n\t{0}", ex.Message);
        return null;
    }
    return null;
}
~~~



[C#中Directory.GetFiles() 函数的使用方法(读取目录中的文件)_HsuanKeys的博客-CSDN博客_directory.getfiles](https://blog.csdn.net/qq_35970739/article/details/82887314)

[.NET API 浏览器 | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/api/?view=net-7.0)

[C#--try catch（异常处理）_@Herry的博客-CSDN博客_c# try catch](https://blog.csdn.net/aimin_com/article/details/80285171)

[AutoCAD .NET 二次开发实例(11) Excel数据和CAD表格数据的同步更新 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/158569412)

## ISO Symbol

[Create IsoSphereSymbol in Plant3d using ObjectARX - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2012/09/create-isospheresymbol-in-plant3d-using-objectarx.html)



##  Create Pipe

[insert a part at a point from plant3d pallette to drawing space by C# .NET - Autodesk Community - AutoCAD Plant 3D](https://forums.autodesk.com/t5/autocad-plant-3d-forum/insert-a-part-at-a-point-from-plant3d-pallette-to-drawing-space/m-p/10468818)

[Wrong size when creating pipes by API - Autodesk Community - AutoCAD Plant 3D](https://forums.autodesk.com/t5/autocad-plant-3d-forum/wrong-size-when-creating-pipes-by-api/m-p/3922304)



## Create Equipment Subpart

[Create Equipment Subpart via API](https://forums.autodesk.com/t5/autocad-plant-3d-forum/create-equipment-subpart-via-api/m-p/8764564)

[Solved: Create Equipment Subpart via API - Autodesk Community - AutoCAD Plant 3D](https://forums.autodesk.com/t5/autocad-plant-3d-forum/create-equipment-subpart-via-api/m-p/8764564)



## Reset Pipe Tags

[Reset Pipe tags - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2015/03/reset-pipe-tags.html)

[AutoCAD PID/Plant 3D: Is there any API to recalculate Tags? - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2012/10/autocad-pidplant-3d-is-there-any-api-to-recalculate-tags.html)
