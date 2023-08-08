---
title: Plant3D API-Plant3D Tools
date: 2023-02-21 21:10:28
tags:
cover: https://s3.bmp.ovh/imgs/2023/01/21/b9b5ab8e8b18eac5.jpg
---

#  Plant3D API - Plant3D Tools

https://zhuanlan.zhihu.com/p/431105940

##  Ribbon Menu

```c#
        // reference article:https://blog.csdn.net/hisinwang/article/details/78797068
        // reference article:https://blog.csdn.net/hisinwang/article/details/78797087
        // reference article:https://blog.csdn.net/wang2543311/article/details/124477198?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EOPENSEARCH%7ERate-2-124477198-blog-78797068.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EOPENSEARCH%7ERate-2-124477198-blog-78797068.pc_relevant_default&utm_relevant_index=5
        public static RibbonTab p3dToolRibbonTab = null;
        [CommandMethod("Plant3DToolRibbonMenu")]
        public void Plant3DToolRibbonMenu()
        {
            string currentDllLocation = Path.GetDirectoryName(this.GetType().Assembly.Location) + "\\";
            // 创建Ribbon Tab页
            if (p3dToolRibbonTab == null)
            {
                p3dToolRibbonTab = new RibbonTab();
                p3dToolRibbonTab.Title = "Plant3D Tools";
                p3dToolRibbonTab.Id = "Plant3DToolsRibbonTab";

                //
                RibbonPanel panel1 = new RibbonPanel();
                RibbonPanelSource panel1Src = new RibbonPanelSource();
                panel1Src.Title = "Fitting";
                panel1.Source = panel1Src;
                p3dToolRibbonTab.Panels.Add(panel1);

                // 添加Fitting Edit Button
                RibbonButton rbnBtn_FittingEdit = NewRibbonBtn("Fitting", "FittingEdit ");
                panel1Src.Items.Add(rbnBtn_FittingEdit);
                RibbonToolTip ribbonToolTip_FittingEdit = new RibbonToolTip();
                ribbonToolTip_FittingEdit.Title = "Fitting Edit";
                ribbonToolTip_FittingEdit.Content = "Edit Fitting Parameters";
                ribbonToolTip_FittingEdit.Command = "FittingEdit";
                ribbonToolTip_FittingEdit.ExpandedContent = "直接修改元件参数,但不用通过元件库";
                string imgBtn_FittingEdit = string.Format("{0}\\{1}\\{2}.png", currentDllLocation, "Images", "FittingEdit");
                Uri uriBtn_FittingEdit = new Uri(imgBtn_FittingEdit);
                BitmapImage bitmapImg_FittingEdit = new BitmapImage(uriBtn_FittingEdit);
                // 按钮图片 16*16
                rbnBtn_FittingEdit.Image = bitmapImg_FittingEdit;
                // 按钮图片 32*32
                rbnBtn_FittingEdit.LargeImage = bitmapImg_FittingEdit;
                rbnBtn_FittingEdit.ShowImage = true;
                rbnBtn_FittingEdit.Size = RibbonItemSize.Large;
                rbnBtn_FittingEdit.Orientation = System.Windows.Controls.Orientation.Vertical;
                rbnBtn_FittingEdit.Text = "FittingEdit";
                rbnBtn_FittingEdit.ShowText = true;



                RibbonButton rbnBtnCircle = NewRibbonBtn("圆", "circle ");
                panel1Src.Items.Add(rbnBtnCircle);

                //
                RibbonPanel panel2 = new RibbonPanel();
                RibbonPanelSource panel2Src = new RibbonPanelSource();
                panel2Src.Title = "Panel 2";
                panel2.Source = panel2Src;
                p3dToolRibbonTab.Panels.Add(panel2);

                RibbonButton rbnBtnTest = NewRibbonBtn("Test", "MyCmdTest ");
                panel2Src.Items.Add(rbnBtnTest);
            }

            // 在AutoCAD的Ribbon窗口中显示
            RibbonControl ribCntrl = RibbonServices.RibbonPaletteSet.RibbonControl;
            bool isShow = false;
            foreach (RibbonTab rbnTab in ribCntrl.Tabs)
            {
                if (rbnTab == p3dToolRibbonTab)
                {
                    isShow = true;
                    break;
                }
            }
            if (!isShow)
            {
                ribCntrl.Tabs.Add(p3dToolRibbonTab);
            }
        }

        [CommandMethod("MyCmdTest")]
        public static void MyCmdTest()
        {
            Document doc = Autodesk.AutoCAD.ApplicationServices.Application.DocumentManager.MdiActiveDocument;
            doc.Editor.WriteMessage("www.caxdev.com\n");
        }

        private static RibbonButton NewRibbonBtn(string text, string cmd)
        {
            RibbonButton button = new RibbonButton();
            button.Text = text;
            button.ShowText = true;
            button.CommandHandler = new AdskCommonHandler();
            button.CommandParameter = cmd;
            return button;
        }

// ------------------------------------------------------------------------------------
// ------------------------------------------------------------------------------------
// ------------------------------------------------------------------------------------
// Functions Coding

        // Edit fitting
        //
        [CommandMethod("FittingEdit")]
        public static void FittingEdit()
        {
            Editor ed = AcadApp.DocumentManager.MdiActiveDocument.Editor;

            try
            {
                // Select part
                //
                ObjectId id = ObjectId.Null;
                while (true)
                {
                    PromptEntityResult res = ed.GetEntity("\nSelect parametric part: ");
                    if (res.Status == PromptStatus.OK)
                    {
                        // Fitting, equipment and support
                        //
                        if (res.ObjectId.ObjectClass.IsDerivedFrom(RXObject.GetClass(typeof(PipeInlineAsset))) ||
                            res.ObjectId.ObjectClass.IsDerivedFrom(RXObject.GetClass(typeof(Support))) ||
                            res.ObjectId.ObjectClass.IsDerivedFrom(RXObject.GetClass(typeof(Equipment))))
                        {
                            // Yes. Take it
                            //
                            id = res.ObjectId;
                            break;
                        }
                    }
                    else
                    if (res.Status == PromptStatus.Cancel)
                    {
                        return;
                    }
                }

                // Find parameters
                //
                ParametricPart param = null;
                try
                {
                    param = new ParametricPart(id);
                }
                catch (System.Exception ex)
                {
                    if (ex.Message != null)
                    {
                        ed.WriteMessage(ex.Message);
                    }
                    ed.WriteMessage("\nPart has no parameters.");
                    return;
                }

                ParameterList plist = param.Parameters;
                if (plist == null || plist.Count == 0)
                {
                    ed.WriteMessage("\nPart has no parameters.");
                    return;
                }

                // Ask for a parameter to edit
                //
                PromptKeywordOptions opts = new PromptKeywordOptions("\nSelect Parameter:");
                while (true)
                {
                    // Kwords
                    //
                    opts.Keywords.Clear();
                    foreach (ParameterInfo pi in plist)
                    {
                        // Kill underscore
                        //
                        String locKwd = pi.Name.Replace("_", "");
                        String globKwd = pi.ToDefinitionString().Replace("_", "");
                        opts.Keywords.Add(locKwd, globKwd);
                    }
                    opts.Keywords.Add("eXit");

                    // Ask
                    //
                    PromptResult res = ed.GetKeywords(opts);
                    if (res.Status != PromptStatus.OK)
                    {
                        return;
                    }
                    if (res.StringResult == /*NOXLATE*/"eXit")
                    {
                        return;
                    }

                    // Find selected parameter
                    //
                    for (int j = 0; j < opts.Keywords.Count; j++)
                    {
                        if (res.StringResult == opts.Keywords[j].GlobalName)
                        {
                            ParameterInfo pi = plist[j];

                            // Ask for a new value
                            //
                            PromptStringOptions sopts = new PromptStringOptions(String.Format("{0}=", pi.Name));
                            sopts.DefaultValue = pi.Value;
                            PromptResult sres = ed.GetString(sopts);

                            if (sres.Status == PromptStatus.OK)
                            {
                                pi.Value = sres.StringResult;

                                // Todo: validate
                                //

                                // Update entity
                                //
                                if (!param.UpdateEntity(id, plist))
                                {
                                    ed.WriteMessage("\nFitting update failed.");
                                }
                            }
                            break;
                        }
                    }
                }
            }
            catch (System.Exception ex)
            {
                if (ex.Message != null)
                {
                    ed.WriteMessage(ex.Message);
                }
            }
        }

    }

    public class AdskCommonHandler : ICommand
    {
        public event EventHandler CanExecuteChanged;

        public bool CanExecute(object parameter)
        {
            return true;
        }

        public void Execute(object parameter)
        {
            RibbonButton button = parameter as RibbonButton;
            if (button != null)
            {
                Autodesk.AutoCAD.ApplicationServices.Core.Application.DocumentManager.MdiActiveDocument.SendStringToExecute(
                    (string)button.CommandParameter, true, false, true);
            }
        }
    }
```

##  Get File Via FileSelectDialog

``` c#
        public void GetDialogToExcel()
        {
            try
            {               
                OpenFileDialog fileDialog = new OpenFileDialog();   //选择文件对话框 
                fileDialog.Multiselect = false;                     //可不可以同时选几个文件
                fileDialog.Title = "Please select a excel file";
                fileDialog.Filter = "Excel Office2007及以上(*.xlsx)|*.xlsx|Excel Office97-2003(*.xls)|*.xls";
                DialogResult result = fileDialog.ShowDialog();
                if (result == DialogResult.OK)
                {
                    if (System.IO.File.Exists(fileDialog.FileName))    //验证路径是否为空或非法
                    {
                        Editor ed = Autodesk.AutoCAD.ApplicationServices.Application.DocumentManager.MdiActiveDocument.Editor;
                        ed.WriteMessage("\n\t{0}", fileDialog.FileName);
                        //return fileDialog.FileName;
                    }
                }
            }
            catch(System.Exception ex)
            {
                Editor ed = Autodesk.AutoCAD.ApplicationServices.Application.DocumentManager.MdiActiveDocument.Editor;
                ed.WriteMessage("\n\t{0}", ex.Message);
                //return null;
            }
            //return null;
        }
```

##  Create Pipe Line With Line Number and Data

###  Get Data From Excel

[VS C#读取和写入excel表数据(手把手教) - 简书 (jianshu.com)](https://www.jianshu.com/p/573b0a6c3a15)

``` c#
        public static System.Collections.Generic.Dictionary<string, System.Collections.Generic.Dictionary<string, string>> GetLineNumberData(string filePath)
        {
            System.Collections.Generic.Dictionary < string,System.Collections.Generic.Dictionary<string,string>> lineNumberData = new System.Collections.Generic.Dictionary<string, System.Collections.Generic.Dictionary<string, string>>();
            NPOI.SS.UserModel.IWorkbook workbook=null;
            NPOI.SS.UserModel.ISheet sheet = null;
            int rowIndex = 1;
            int colIndex = 0;
            try
            {
                FileStream fs = new FileStream(filePath, FileMode.Open, FileAccess.Read);
                if (filePath.IndexOf(".xlsx") > 0)  //2007版本
                {
                    workbook = new NPOI.XSSF.UserModel.XSSFWorkbook(fs);
                }
                else if(filePath.IndexOf(".xls") > 0) // 2003版本
                {
                    workbook = new NPOI.HSSF.UserModel.HSSFWorkbook(fs);
                }

                sheet = workbook.GetSheet("HHEL_管线号 - 图形数据");
                if (sheet == null)
                {
                    sheet = workbook.GetSheetAt(0);//获取第几个sheet表（此处表示如果没有给定sheet名称，默认是第一个sheet表）
                }

                if (sheet != null)
                {
                    NPOI.SS.UserModel.IRow titleRow = sheet.GetRow(0);
                    int lastCol = titleRow.LastCellNum;
                    //for (rowIndex=1; rowIndex < titleRow.LastCellNum; rowIndex++)   //获取最后一列
                    //{
                    while (sheet.GetRow(rowIndex).GetCell(1)!=null)
                    {
                        NPOI.SS.UserModel.IRow crow = sheet.GetRow(rowIndex);
                        System.Collections.Generic.Dictionary<string, string> eachLine =new System.Collections.Generic.Dictionary<string, string>();
                        for (colIndex=2;colIndex< titleRow.LastCellNum; colIndex++)
                        {
                            eachLine.Add(titleRow.GetCell(colIndex).StringCellValue, crow.GetCell(colIndex).StringCellValue);
                        }
                        lineNumberData.Add(crow.GetCell(1).StringCellValue, eachLine);
                        rowIndex++;
                    }
                }
            }
            catch(System.Exception ex)
            {

            }


            return lineNumberData;

        }
    }
```



###  Add LineNumber

### Create Line

### Add 3D Text to Pipe as Mark

####  Create 3D Text

[Solved: 3D Text on Plant3D Pipe via .NET API - Autodesk Community - AutoCAD Plant 3D](https://forums.autodesk.com/t5/autocad-plant-3d-forum/3d-text-on-plant3d-pipe-via-net-api/m-p/9440775)

[C#实战之CAD二次开发007：根据用户输入和选择来插入图块和文字_cad二次开发 插入文字_淡淡星辰丶的博客-CSDN博客](https://blog.csdn.net/qq_26739139/article/details/103481182?ops_request_misc=%7B%22request%5Fid%22%3A%22167856001316800226585673%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=167856001316800226585673&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-103481182-null-null.142^v73^control,201^v4^add_ask,239^v2^insert_chatgpt&utm_term=cad二次开发 插入文字&spm=1018.2226.3001.4187)

####  Insert MText

[C#实战之CAD二次开发003：插入文字和插入图块_cad二次开发 插入文字_淡淡星辰丶的博客-CSDN博客](https://blog.csdn.net/qq_26739139/article/details/93471294)



##  Multithreading

##  Get Data From Excel

##  ./RibbonUI.bundle/PackageContents.xml        

            ```
            
            <?xml version="1.0" encoding="utf-8" ?>
            <ApplicationPackage SchemaVersion="1.0" AppVersion="1.0"
                ProductCode="{4aaf74a4-aa80-406c-8f03-16b9a3a5d376}"
                Name="Plant3DTools2021"
            >
              <CompanyDetails
                Name="****"
                Email="****"
              />
              <RuntimeRequirements OS="Win32|Win64" Platform="AutoCAD*" />
              <Components>
             <ComponentEntry ModuleName="./debug/Plant3DTools2021.dll">
            	  <Commands>
                    <Command Local="Plant3DToolRibbonMenu" Global="Plant3DToolRibbonMenu" StartupCommand="True"/>
                  </Commands>
            	</ComponentEntry>
              </Components>
             
            </ApplicationPackage>
            ```



- ```
  问：cad二次开发dll如何使用沙盒
  ```

- ```
  答：如果您想在CAD中使用二次开发的DLL，您可以使用.NET的沙箱技术来提高安全性。下面是一些基本的步骤，您可以按照以下步骤来实现：
  
  1. 在Visual Studio中创建一个新的.NET项目，选择"C# Class Library"。
  
  2. 使用CAD的API（例如AutoCAD、BricsCAD等）来编写您的DLL，用于您想要在CAD中实现的功能。
  
  3. 在项目中添加System.Security.Permissions和System.CodeDom.Compiler命名空间。
  
  4. 在程序集的条目清单文件中（例如，（你的程序名）.manifest）中定义所需的权限。例如，你可以使用以下代码来定义一个沙箱：
  
    <?xml version="1.0" encoding="UTF-8"?>
    <assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">
    	<trustInfo xmlns="urn:schemas-microsoft-com:asm.v3">
    		<security>
    			<requestedPrivileges>
    				<requestedExecutionLevel level="asInvoker" uiAccess="false"/>
    			</requestedPrivileges>
    		</security>
    	</trustInfo>
    </assembly>
  
  5. 在项目中添加代码，以创建并使用沙箱来运行DLL。您可以使用以下代码实现它：
  
    string assemblyFile = "YourDLL.dll";
  
    // Create a new sandbox domain with the permission set
    AppDomainSetup setup = new AppDomainSetup();
    setup.ApplicationBase = AppDomain.CurrentDomain.BaseDirectory;
  
    PermissionSet permissions = new PermissionSet(PermissionState.None);
    permissions.AddPermission(new SecurityPermission(SecurityPermissionFlag.Execution));
    permissions.AddPermission(new FileIOPermission(FileIOPermissionAccess.Read | FileIOPermissionAccess.PathDiscovery, assemblyFile));
  
    AppDomain sandbox = AppDomain.CreateDomain("sandbox", null, setup, permissions);
  
    // Load the assembly in the sandbox
    sandbox.Load(AssemblyName.GetAssemblyName(assemblyFile));
  
    // Create an instance of the class in the sandbox and call a method on it
    object instance = sandbox.CreateInstanceAndUnwrap("YourDLL", "YourDLL.ClassName");
    instance.MyMethod();
  
  6. 在CAD中载入您的DLL（按照您所使用的不同CAD软件而异），并测试其是否在沙箱中安全运行。
  ```

- ```
  问：举一个完整的示例
  ```

- ```
  答：好的，以下是一个完整的示例，演示如何在AutoCAD中使用二次开发DLL，并在.NET沙箱中运行以提高安全性。
  
  1. 在Visual Studio中，创建一个新的"C# Class Library"项目，将项目命名为"MyAutoCADExtension"。
  
  2. 将AutoCAD的API添加到项目中。 单击“项目”菜单，选择“添加引用”，并在“COM”选项卡中选择“AutoCAD 20XX Type Library”。
  
  3. 将以下代码添加到Class1.cs文件中：
  
     using Autodesk.AutoCAD.Runtime;
     using System.Security.Permissions;
  
     namespace MyAutoCADExtension
     {
         [PermissionSet(SecurityAction.Demand, Name = "FullTrust")]
         public class MyCommands
         {
             [CommandMethod("MyCommand")]
             public void MyCommand()
             {
                 Autodesk.AutoCAD.ApplicationServices.Application.ShowAlertDialog("Hello from MyAutoCADExtension!");
             }
         }
     }
  
  4. 单击“生成”菜单，然后单击“生成解决方案”。
  
  5. 通过Visual Studio“输出”窗口找到MyAutoCADExtension.dll文件的完整路径。
  
  6. 打开记事本或其他文本编辑器，并将以下代码复制并粘贴到新文件中，保存为“mysandbox.manifest”：
  
    <?xml version="1.0" encoding="utf-8" ?>
    <assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">
     <trustInfo xmlns="urn:schemas-microsoft-com:asm.v2">
     <security>
       <requestedPrivileges xmlns="urn:schemas-microsoft-com:asm.v3">
         <requestedExecutionLevel level="asInvoker" uiAccess="false" />
       </requestedPrivileges>
     </security>
     </trustInfo>
   </assembly>
  
  7. 安装MyAutoCADExtension.dll和mysandbox.manifest文件，方法如下：
  
     a. 将MyAutoCADExtension.dll文件复制到AutoCAD支持文件的一种文件夹位置，例如"C:\Program Files\AutoCAD 20XX\Support"。
  
     b. 将mysandbox.manifest文件复制到MyAutoCADExtension.dll文件的相同位置。
  
  8. 启动AutoCAD，并在命令行中输入netload命令，然后浏览到MyAutoCADExtension.dll的位置并加载它（例如，“C:\Program Files\AutoCAD 20XX\Support\MyAutoCADExtension.dll”）。
  
  9. 在命令行中输入MyCommand，并观察是否成功执行。
  
  这个示例演示了如何将AutoCAD的API添加到.NET项目中，并使用命令方法将C＃功能发布到AutoCAD的命令行。此外，为了提高安全性，还演示了如何使用.NET沙箱技术将DLL限制为可以执行的最低特权。
  ```
