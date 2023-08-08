---
title: Plant3D API(2)
date: 2023-02-05 02:26:23
tags: 
cover: https://s3.bmp.ovh/imgs/2023/01/21/b9b5ab8e8b18eac5.jpg
---

# Plant3D API(2)--Create Pipeline as linenumber

##  Target

Create a set of pipeline according a list of line number from an excel form and set the line number tag to the each pipe line, then import the custom properties of linegroup to database

> [Getting all the Properties of a selected Pipe in Plant3d using C#.NET - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2012/05/getting-all-the-properties-of-a-selected-pipe-in-plant3d-using-cnet.html)

##  Stpe 1: Create Pipeline

``` c#
public class Program
    {
        [CommandMethod("CreatePipeline")]

        public static void CreatePipeline()
        {
            Database db = AcadApp.DocumentManager.MdiActiveDocument.Database;
            Editor ed = AcadApp.DocumentManager.MdiActiveDocument.Editor;
            ContentManager cm = ContentManager.GetContentManager();
            Project currentProject = PlantApp.CurrentProject.ProjectParts["Piping"];
            DataLinksManager dlm = currentProject.DataLinksManager;
            DataLinksManager3d dlm3d = DataLinksManager3d.Get3dManager(dlm);
            PipingObjectAdder pipeObjAdder = new PipingObjectAdder(dlm3d, db);
            Point3d nextPartPos = Point3d.Origin; // We will start pipe-routing from Origin.

            // Set filters to find the correct spec part. For now, we just need to find one 6" (NomDiameter value) pipe.
            //
            System.Collections.Specialized.StringCollection pipePropertyNames = new System.Collections.Specialized.StringCollection();
            System.Collections.Specialized.StringCollection pipePropertyValues = new System.Collections.Specialized.StringCollection();
            pipePropertyNames.Add("NominalDiameter");
            pipePropertyValues.Add(NomDiameter.ToString());

            // Fetch spec-part PIPE 1
            //
            SpecPart specPart_Pipe1 = FetchSpecPart("Pipe", pipePropertyNames, pipePropertyValues);

            // Create part entity PIPE 1
            //
            Pipe pipeEntity_Pipe1 = CreatePipe();

            // Set part entity position PIPE1
            pipeEntity_Pipe1.StartPoint = nextPartPos;
            pipeEntity_Pipe1.EndPoint = nextPartPos.Add(new Vector3d(60, 0, 0));
            pipeEntity_Pipe1.OuterDiameter = (double)specPart_Pipe1.PropValue("MatchingPipeOd");

            // Add the PIPE 1 part to the database
            //
            ObjectId objId_Pipe1 = AddObjectToDatabase(specPart_Pipe1, pipeEntity_Pipe1, String.Empty, null, ref db, ref ed, ref currentProject, ref dlm, ref dlm3d, ref pipeObjAdder);



            // Fetch the postion of the NEXT part
            //
            //nextPartPos = FetchNextPartsPosition(objId_Pipe1, 1, db);

        }
        public static ObjectId AddObjectToDatabase(
            SpecPart specPart,
            Autodesk.ProcessPower.PnP3dObjects.Part part,
            String connectionName,
            PartSizePropertiesCollection connectionPropColl,
            ref Database db,
            ref Editor ed,
            ref Project currentProject,
            ref DataLinksManager dlm,
            ref DataLinksManager3d dlm3d,
            ref PipingObjectAdder pipeObjAdder)
        {
            ObjectId partObjectId = ObjectId.Null;
            if (pipeObjAdder == null)
            {
                ed.WriteMessage("Error: Cannot create PipingObjectAdder");
                return partObjectId;
            }

            Autodesk.AutoCAD.DatabaseServices.TransactionManager tm = db.TransactionManager;
            try
            {
                using (Transaction trans = tm.StartTransaction())
                {
                    if (part.GetType() == typeof(Pipe))
                    {
                        Pipe pipe = part as Pipe;
                        pipeObjAdder.Add(specPart, pipe);
                    }
                    else if (part.GetType() == typeof(PipeInlineAsset))
                    {
                        PipeInlineAsset pipeInlineAsset = part as PipeInlineAsset;
                        pipeObjAdder.Add(specPart, pipeInlineAsset);
                    }
                    else if (part.GetType() == typeof(Connector))
                    {
                        Connector connector = part as Connector;
                        pipeObjAdder.Add(connectionName, connectionPropColl, connector);
                    }

                    partObjectId = part.ObjectId;
                    trans.AddNewlyCreatedDBObject(part, true);
                    trans.Commit();
                }
            }
            catch (SystemException ex)
            {
                ed.WriteMessage("Error: Exception while appending objects.\n");
                ed.WriteMessage(ex.Message);
            }
            return partObjectId;
        }
        public static SpecPart FetchSpecPart(
            String PartName,
            System.Collections.Specialized.StringCollection PropertyNames,
            System.Collections.Specialized.StringCollection PropertyValues)
                {
                    // spec mananger object of the active project
                    var specMgr = SpecManager.GetSpecManager();

                    SpecPart specPart = null;
                    if (specMgr.HasType(SpecName, PartName))
                    {
                        SpecPartReader specPartReader = specMgr.SelectParts(SpecName, PartName, PropertyNames, PropertyValues);
                        while (specPartReader.Next())
                        {
                            specPart = specPartReader.Current;
                            var partND = specPart.NominalDiameter.Value;

                            if (specPart.Type.Equals(PartName) &&
                                partND == NomDiameter.Value) // matching part found
                            {
                                break;
                            }
                        }
                    }

                    // save the spectpart object
                    //
                    return specPart;
                }

        public static Pipe CreatePipe()
        {
            Pipe pipePart = new Pipe();
            return pipePart;
        }
        static String SpecName
        {
            get
            {
                return "CS2";
            }
        }
        static NominalDiameter NomDiameter
        {
            get
            {
                return new NominalDiameter("mm", 150.0);
            }
        }
    }
```



##  Stpe 2: Create  And Assign A New LineGroup to a Pipe

>  <strong>[Create a new LineGroup in Plant3d - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2013/07/create-a-new-linegroup-in-plant3d.html)</strong>

``` c#
public static void CreateAndAssignLineGroup(ref PipingProject prjpart,ref ObjectId partId)
        {
            //Assign an entity's row to line group
            //
            DataLinksManager dlm = prjpart.DataLinksManager;
            Autodesk.ProcessPower.DataObjects.PnPDatabase db = dlm.GetPnPDatabase();

            // Create a new line group in project database
            //
            Autodesk.ProcessPower.DataObjects.PnPTable tbl = db.Tables["P3dLineGroup"];
            Autodesk.ProcessPower.DataObjects.PnPRow row = tbl.NewRow();
            tbl.Rows.Add(row);

            // Relate objects to line group
            //
            int cacheId = dlm.FindAcPpRowId(partId);
            dlm.Relate("P3dLineGroupPartRelationship", "LineGroup", row.RowId, "Part", cacheId);

            // Also relate drawing to line group
            //
            Database acdb = partId.Database;
            int dwgId = dlm.GetDrawingId(acdb);
            if (dwgId != -1)
            {
                dlm.Relate("P3dDrawingLineGroupRelationship", "Drawing", dwgId, "LineGroup", row.RowId);
            }

        }
```

**Problem：**What is difference between PipingProject(```  class Autodesk.ProcessPower.P3dProjectParts.PipingProject``` ) and ProjectManager's Project(```class Autodesk.ProcessPower.ProjectManager.Project```)? From Step 1's code, we find that pipe line should be eidted via the latter one. Line group should be edit via the former one from Step 2's code.

Note:

## Step 3: Setting the Line Number Tag

> <strong>[Setting the Line Number Tag in AutoCAD Plant3d - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2012/08/setting-the-line-number-tag-in-autocad-plant3d.html)</strong>
>
> <strong>[Mimic Line Number New feature on Plant 3D 2.0 - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2015/07/mimic-line-number-new-feature-on-plant-3d-20.html)</strong>
>
> <strong>[Mimic Line Number New feature on Plant 3D - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2015/04/mimic-line-number-new-feature-on-plant-3d.html)</strong>

##  Step 4: Import Custom Properties Of Linegroup To Database

##  An LineGroup Assigning To Pipe Example

~~~c#
// This a example code showing to relate a linegroup to an entity object

public void AssignLineGroupOfPipeToSupport()
        {
            PlantProject plantproj = PlantApplication.CurrentProject;
            Autodesk.ProcessPower.P3dProjectParts.PipingProject proj = 					 (Autodesk.ProcessPower.P3dProjectParts.PipingProject)plantproj.ProjectParts["Piping"];
            DataLinksManager dlm = proj.DataLinksManager;

            Editor ed = Autodesk.AutoCAD.ApplicationServices.Application.DocumentManager.MdiActiveDocument.Editor; ;
            Autodesk.ProcessPower.PnP3dDataLinks.DataLinksManager3d dlm3d = proj.DataLinksManager3d;

            Autodesk.ProcessPower.PnP3dObjects.Pipe pipe = new Autodesk.ProcessPower.PnP3dObjects.Pipe();
            Autodesk.ProcessPower.PnP3dObjects.Pipe pipe2 = new Autodesk.ProcessPower.PnP3dObjects.Pipe();
            
            int PipeRowId = dlm.FindAcPpRowId(pipe.ObjectId);

            PnPRowIdArray lgid = dlm.GetRelatedRowIds("P3dLineGroupPartRelationship", "Part", PipeRowId, "LineGroup");
            
            if(lgid.Count==0)
            {
                ed.WriteMessage("\nNo LineGroup relation to pipe defined!");
            }
            else
            {
                System.Collections.IEnumerator enumerator = lgid.GetEnumerator();
                while(enumerator.MoveNext())
                {
                    int lgRowId = (int)enumerator.Current;
                    int pipe2RowId = dlm.FindAcPpRowId(pipe2.ObjectId);
                    dlm.Relate("P3dLineGroupPartRelationship", "LineGroup", lgRowId, "Part", pipe2RowId);

                }

            }

        }
~~~

##  



> reference article
>
> [API how to assign the Norminal Size/Spec, Insulation Type/Thickness?](https://forums.autodesk.com/t5/autocad-plant-3d-forum/api-how-to-assign-the-norminal-size-spec-insulation-type/m-p/5816877)
>
> [Solved: 3D Text on Plant3D Pipe via .NET API - Autodesk Community - AutoCAD Plant 3D](https://forums.autodesk.com/t5/autocad-plant-3d-forum/3d-text-on-plant3d-pipe-via-net-api/m-p/9440775)
>
> [Solved: API, How to get P3D object by LineNumber? - Autodesk Community - AutoCAD Plant 3D](https://forums.autodesk.com/t5/autocad-plant-3d-forum/api-how-to-get-p3d-object-by-linenumber/m-p/5818840)
>
> [Reset Pipe tags - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2015/03/reset-pipe-tags.html)
>
> 
>
> 
>
> <strong>[Create a new LineGroup in Plant3d - AutoCAD DevBlog (typepad.com)](https://adndevblog.typepad.com/autocad/2013/07/create-a-new-linegroup-in-plant3d.html)</strong>
>
> https://help.autodesk.com/view/PLNT3D/2018/ENU/?guid=GUID-74FE3BA8-DC06-43DC-80E3-39AAA876FE0A
>
> https://forums.autodesk.com/t5/autocad-plant-3d-forum/api-help/m-p/6031694/highlight/true#M20611



[TOC]

