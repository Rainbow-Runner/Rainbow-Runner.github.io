---
title: Plant3DAPI-Examples
date: 2023-03-12 05:30:12
tags:
cover: https://s3.bmp.ovh/imgs/2023/01/21/b9b5ab8e8b18eac5.jpg
---

```c#
public static void GetLinenumberProperties()
        {
            Document doc = Autodesk.AutoCAD.ApplicationServices.Application.DocumentManager.MdiActiveDocument;
            Database db = doc.Database;
            Editor ed = doc.Editor;

            // We get the project piping database
            PlantProject plantproj = PlantApplication.CurrentProject; ;
            Project proj = plantproj.ProjectParts["Piping"];
            DataLinksManager dlm = proj.DataLinksManager;
            PnPDatabase pipingdb = dlm.GetPnPDatabase();

            // We query for all linenumbers in the piping gatabase.
            // Each linelimber is a row in 'P3dLineGroup' table
            PnPRow[] linegroups = pipingdb.Tables["P3dLineGroup"].Select();
            foreach (PnPRow linegroup in linegroups)
            {
                // We skip the 'unnamed' linegroups
                if (linegroup["Tag"].ToString() == "?")
                {
                    continue;
                }

                // We make a header for linenumber data
                ed.WriteMessage("\n-------------{0}-------------", linegroup["Tag"]);

                // We output to editor the the values of the fields 
                // in the current row in 'P3dLineGroup' table
                foreach (PnPColumn col in pipingdb.Tables["P3dLineGroup"].Columns)
                {
                    // We skip the empty or 'null' values
                    if (string.Format("{0}", linegroup[col.Name]) == "")
                    {
                        continue;
                    }

                    // Now we output to editor the value 
                    // of the current field
                    ed.WriteMessage("\n{0} : {1}", col.Name, linegroup[col.Name]);
                }
            }
        }
```

