---
title: Plant3D API - Get All Valves
date: 2023-02-20 21:26:13
tags:
cover: https://s3.bmp.ovh/imgs/2023/01/21/b9b5ab8e8b18eac5.jpg
---

 #  Plant3D API - Get All Valves

##  Example Code1 --  Get all the Properties of a selected Pipe

[Getting all the Properties of a selected Pipe in Plant3d using C#.NET - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2012/05/getting-all-the-properties-of-a-selected-pipe-in-plant3d-using-cnet.html)

```python
// get all the properties of a selected object in Plant3d

// by Fenton Webb, Autodesk, 29/05/2012

[CommandMethod("GetPipingProperties")]

public void GetPipingProperties()

{

  // get the AutoCAD Editor object so we can print to the command line
  Editor ed = AcadApp.DocumentManager.MdiActiveDocument.Editor;
 

  // select the object
  PromptEntityResult res = ed.GetEntity("Pick Object obtain properties : ");

  // if something selected
  if (res.Status == PromptStatus.OK)
  {
    // get the current project object
    PlantProject currentProj = PlantApplication.CurrentProject;

    // get the Piping project part
    PipingProject pipingProj = (PipingProject)currentProj.ProjectParts["Piping"];

    // get the data links manager
    DataLinksManager dlm = pipingProj.DataLinksManager;

    // and then get the row id for the selected object
    int rowId = dlm.FindAcPpRowId(res.ObjectId);

    List<KeyValuePair<string, string>> properties;
    properties = dlm.GetAllProperties(rowId, true);

    // Iterate through the entries in the list.
    for (int i = 0; i < properties.Count; i++)
      ed.WriteMessage("\nProperty name:" + properties[i].Key + " = " + properties[i].Value);
  }
}
```

![](https://s3.bmp.ovh/imgs/2023/02/20/b506e8092dd1c0c3.png)
