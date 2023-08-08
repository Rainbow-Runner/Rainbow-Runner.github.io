---
title: DWGorDXFtoImage
date: 2023-02-28 00:41:10
tags:
cover: https://s3.bmp.ovh/imgs/2023/01/21/b9b5ab8e8b18eac5.jpg
---

[C# DWG or DXF to PNG JPEG BMP TIFFand GIF Image | C# CAD API (aspose.com)](https://blog.aspose.com/2020/09/28/dwg-dxf-to-png-jpeg-bmp-tiff-gif-using-csharp/)

[GitHub - Hamza442004/DXF2img: A tool to convert dxf files to images](https://github.com/Hamza442004/DXF2img)

[python实现高质量PDF转PNG - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/102742847)

[【PyMuPDF和pdf2image】Python将PDF转成图片PNG和JPG - 简书 (jianshu.com)](https://www.jianshu.com/p/f57cc64b9f5e)

``` python
import sys, fitz, os, datetime

def pyMuPDF_fitz(pdfPath, imagePath):
    startTime_pdf2img = datetime.datetime.now()#开始时间

    pdfDoc = fitz.open(pdfPath)
    for pg in range(pdfDoc.page_count):
        page = pdfDoc[pg]
        rotate = int(0)
        # 每个尺寸的缩放系数为1.3，这将为我们生成分辨率提高2.6的图像。
        # 此处若是不做设置，默认图片大小为：792X612, dpi=96
        zoom_x = 8.8598484848484848484 #(1.33333333-->1056x816)   (2-->1584x1224)
        zoom_y = 8.1094771241830065359
        mat = fitz.Matrix(zoom_x, zoom_y).prerotate(rotate)
        pix = page.get_pixmap(matrix=mat, alpha=False)
        if not os.path.exists(imagePath):#判断存放图片的文件夹是否存在
            os.makedirs(imagePath) # 若图片文件夹不存在就创建

        pix.save(imagePath+'/'+'images_%s.png' % pg)#将图片写入指定的文件夹内

    endTime_pdf2img = datetime.datetime.now()#结束时间
    print('pdf2img时间=',(endTime_pdf2img - startTime_pdf2img).seconds)

def pyMuPDF2_fitz(pdfPath, imagePath):
    pdfDoc = fitz.open(pdfPath) # open document
    for pg in range(pdfDoc.page_count): # iterate through the pages
        page = pdfDoc[pg]
        rotate = int(0)
        # 每个尺寸的缩放系数为1.3，这将为我们生成分辨率提高2.6的图像
        # 此处若是不做设置，默认图片大小为：792X612, dpi=96
        zoom_x = 8.8598484848484848484 #(1.33333333-->1056x816)   (2-->1584x1224)
        zoom_y = 8.1094771241830065359
        # 缩放系数1.3在每个维度  .preRotate(rotate)是执行一个旋转
        mat = fitz.Matrix(zoom_x, zoom_y).prerotate(rotate) 
        
        # # 提取PDF中文本
        # fp= open('C:/Users/Boqibin1/Desktop/dxf/text.txt','w',encoding='utf8')
        # fp.write(page.get_text()+'\n')

        # 添加Shape
        sp1=(1000,-9000)
        sp2=(1455, -7489)
        r = fitz.Rect(sp1, sp2)
        shape=page.new_shape()
        shape.draw_rect(r)
        shape.finish(color=(1, 1, 1), fill=(1, 1, 1))
        shape.commit()
        # pdfDoc.save('C:/Users/Boqibin1/Desktop/dxf/x.pdf')

        rect = page.rect                         # 页面大小

        # title area image
        mp3=rect.br - (437.5,147.2)
        mp4=rect.br - (94.5,92)
        clip2 = fitz.Rect(mp3, mp4)            # 想要截取的区域
        pix2 = page.get_pixmap(matrix=mat, alpha=False, clip=clip2) # 将页面转换为图像

        # insert image
        titleArea=fitz.Rect(10273,368,13125.0, 819.0)
        insertImgTBotRight=(13125.0, 819.0)
        
        # shape2=page.new_shape()
        # shape2.draw_rect(titleArea-(4000,0,4000,0))
        # shape2.finish(color=(0, 0, 1), fill=(1, 1, 0))
        # shape2.commit()
        titleimg = page.insert_image(titleArea-(4000,0,4000,0),pixmap=pix2)#,xref=titleimg)#,  2nd time reuses existing image)
        pdfDoc.save(r'C:\Users\RainbowRunner\Desktop\dxf\x.pdf')

        mp = (134,75.8) # 矩形区域    56=75/1.3333
        mp2 = rect.br - (565,75) # 矩形区域    56=75/1.3333

        clip = fitz.Rect(mp, mp2)            # 想要截取的区域
        pix = page.get_pixmap(matrix=mat, alpha=False, clip=clip) # 将页面转换为图像
        pix_unclip = page.get_pixmap(matrix=mat, alpha=False)



        if not os.path.exists(imagePath):
            os.makedirs(imagePath)
        pix.save(imagePath+'/'+'psReport_%s.png' % pg)# store image as a PNG
        pix2.save(imagePath+'/'+'title%s.png' % pg)# store image as a PNG
        pix_unclip.save(imagePath+'/'+'psReport_unclip%s.png' % pg)# store image as a PNG


if __name__ == "__main__":
    pdfPath = r'C:\Users\RainbowRunner\Desktop\dxf\C039540_400C-03-Model.pdf'
    imagePath = r'C:\Users\RainbowRunner\Desktop\dxf\image'
    # pyMuPDF_fitz(pdfPath, imagePath)#只是转换图片
    pyMuPDF2_fitz(pdfPath, imagePath)#指定想要的区域转换成图片
```

[Rect — PyMuPDF 1.21.1 documentation](https://pymupdf.readthedocs.io/en/latest/rect.html#Rect.top_left)

[Python CAD, Python 将 DWG 转换为 PDF, Libredwg 蟒蛇, Pyautocad, DWG 转 PDF Python, Python DXF 转 PDF, 如何打开 DWG 文件, AutoCAD 文件 Python, DWG 转 DXF, 皮亚卡德, ODA 文件转换器, ezdxf (zditect.com)](https://zditect.com/article/2637038.html#:~:text=使用 Python 或 Go 在 Python 中将 CAD%2FDWG,CAD 格式包括 DWG、DWF、DXF，并且可以使用 PDF 将 DGN 直接转换为 PDF。)

##  Pdf2Img_GUI

``` python
# -*- coding: utf-8 -*-
import matplotlib.pyplot as plt
import ezdxf
from ezdxf.addons.drawing import RenderContext, Frontend
from ezdxf.addons.drawing.matplotlib import MatplotlibBackend
import wx
import glob
import re
import sys, fitz, os, datetime

class PDF2IMAGE(object):
    def pyMuPDF_fitz(self, names,savefolder):
        for name in names:
            # img_nameFolderPath = re.findall("(\S+)\.",name).
            startTime_pdf2img = datetime.datetime.now()#开始时间

            pdfDoc = fitz.open(str(name))
            for pg in range(pdfDoc.page_count):
                page = pdfDoc[pg]
                rotate = int(0)
                # 每个尺寸的缩放系数为1.3，这将为我们生成分辨率提高2.6的图像。
                # 此处若是不做设置，默认图片大小为：792X612, dpi=96
                zoom_x = 1.33333333 #(1.33333333-->1056x816)   (2-->1584x1224)
                zoom_y = 1.33333333
                mat = fitz.Matrix(zoom_x, zoom_y).prerotate(rotate)
                pix = page.get_pixmap(matrix=mat, alpha=False)
                if not os.path.exists(savefolder):#判断存放图片的文件夹是否存在
                    os.makedirs(savefolder) # 若图片文件夹不存在就创建

                pix.save(savefolder+'/'+ os.path.basename(name).split('.')[0] + '_images.png')#将图片写入指定的文件夹内
                

            endTime_pdf2img = datetime.datetime.now()#结束时间
            print('pdf2img时间=',(endTime_pdf2img - startTime_pdf2img).seconds)

    def pyMuPDF2_fitz(self,names,savefolder,saveFirstName,saveName,offsetLen): 
        for name in names:
            pdfDoc = fitz.open(name) # open document
            for pg in range(pdfDoc.page_count): # iterate through the pages
                page = pdfDoc[pg]
                rotate = int(0)
                # 每个尺寸的缩放系数为1.3，这将为我们生成分辨率提高2.6的图像
                # 此处若是不做设置，默认图片大小为：792X612, dpi=96
                zoom_x = 8.8598484848484848484 #(1.33333333-->1056x816)   (2-->1584x1224)
                zoom_y = 8.1094771241830065359
                # 缩放系数1.3在每个维度  .preRotate(rotate)是执行一个旋转
                mat = fitz.Matrix(zoom_x, zoom_y).prerotate(rotate) 

                # 添加Shape遮挡指北
                sp1=(1000,-9000)
                sp2=(1455, -7489)
                r = fitz.Rect(sp1, sp2)
                shape=page.new_shape()
                shape.draw_rect(r)
                shape.finish(color=(1, 1, 1), fill=(1, 1, 1))
                shape.commit()
                # pdfDoc.save('C:/Users/Boqibin1/Desktop/dxf/x.pdf')

                rect = page.rect                         # 页面大小

                # title area image
                mp3=rect.br - (437.5,147.2)
                mp4=rect.br - (94.5,92)
                clip2 = fitz.Rect(mp3, mp4)            # 想要截取的区域
                pixTitle = page.get_pixmap(matrix=mat, alpha=False, clip=clip2) # 将页面转换为图像

                # insert image
                titleArea=fitz.Rect(10273,368,13125.0, 819.0)                
                # shape2=page.new_shape()
                # shape2.draw_rect(fitz.Rect(page.search_for('图纸名称')[0].tl,page.search_for('图纸名称')[0].br))
                # shape2.finish(color=(0, 0, 1), fill=(1, 1, 0))
                # shape2.commit()
                titleimg = page.insert_image(titleArea-(offsetLen,0,offsetLen,0),pixmap=pixTitle)#,xref=titleimg)#,  2nd time reuses existing image)

                # 最终截图
                mp = (134,75.8) # 矩形区域    56=75/1.3333
                mp2 = rect.br - (565,75) # 矩形区域    56=75/1.3333
                clip = fitz.Rect(mp, mp2)            # 想要截取的区域

                pix = page.get_pixmap(matrix=mat, alpha=False, clip=clip) # 将页面转换为图像

                if not os.path.exists(savefolder):
                    os.makedirs(savefolder)
                pix.save(savefolder+'/'+ saveFirstName + os.path.basename(name).split('.')[0]+saveName+'%s.png' % pg)# store image as a PNG
                pixTitle.save(savefolder+'/TitleImg_'+ os.path.basename(name).split('.')[0]+'_image%s.png' % pg)# store image as a PNG


class DXF2IMG(object):
    

    default_img_format = '.png'
    default_img_res = 300
    default_bg_color = '#FFFFFF' #White
    def convert_dxf2img(self, names, img_format=default_img_format, img_res=default_img_res, clr=default_bg_color):
        for name in names:
            doc = ezdxf.readfile(name)
            msp = doc.modelspace()
            # Recommended: audit & repair DXF document before rendering
            auditor = doc.audit()
            # The auditor.errors attribute stores severe errors,
            # which *may* raise exceptions when rendering.
            if len(auditor.errors) != 0:
                raise Exception("This DXF document is damaged and can't be converted! --> ", name)
                name = name =+ 1
            else :
                fig = plt.figure()
                ax = fig.add_axes([0, 0, 1, 1])
                ctx = RenderContext(doc)
                ctx.set_current_layout(msp)
                ezdxf.addons.drawing.properties.MODEL_SPACE_BG_COLOR = clr
                out = MatplotlibBackend(ax)
                Frontend(ctx, out).draw_layout(msp, finalize=True)

                img_name = re.findall("(\S+)\.",name)  # select the image name that is the same as the dxf file name
                first_param = ''.join(img_name) + img_format  #concatenate list and string
                fig.savefig(first_param, dpi=img_res)
                print(name," Converted Successfully")



#================================================================================================
# GUI Section
user_files = list()

class The_GUI(wx.Frame):

    def __init__(self):
        super().__init__(None, title='DXf Converter', size=(400,350),     #define the frame
                        style=wx.MINIMIZE_BOX | wx.RESIZE_BORDER | wx.SYSTEM_MENU |
                         wx.CAPTION | wx.CLOSE_BOX | wx.CLIP_CHILDREN)

        panel = wx.Panel(self)


        first_static = wx.StaticBox(panel, label='Folder Selection',
                            size=(370,200),pos=(5,5))

        self.list_ctrl = wx.ListCtrl(
            panel, size=(-1, 100),name='first_list',
            style=wx.LC_REPORT | wx.BORDER_SUNKEN,
            pos=(20,30))
        self.list_ctrl.InsertColumn(0, 'DXFS', width=300)

        font1 = wx.Font(10, wx.DEFAULT, wx.NORMAL, wx.DEFAULT)
        st1 = wx.StaticText(panel, label='Type Or Select Folder :',
                    style=wx.ALIGN_LEFT, pos=(20,140))
        st1.SetFont(font1)



        self.rtb = wx.TextCtrl(panel,style=wx.TE_PROCESS_ENTER,
                                pos=(20,163),size=(199,25))
        user_folder1 = self.rtb.Bind(wx.EVT_TEXT_ENTER, self.Txt_Ent, id = -1)



        folder_btn = wx.Button(panel,label='Select Folder',pos=(270,166))
        folder_btn.Bind(wx.EVT_BUTTON,self.on_open_folder)

#=============================================================================

        second_static = wx.StaticBox(panel, label='Output',
                        size=(370,150),pos=(5,210))


        font2 = wx.Font(9, wx.DEFAULT, wx.NORMAL, wx.DEFAULT)
        st2 = wx.StaticText(panel, label='Image Format :',
                            style=wx.ALIGN_LEFT, pos=(13,235))
        st2.SetFont(font2)

        formats = ['.png','.pdf','.jpg','.tiff']
        formats_cb = wx.ComboBox(panel, pos=(103, 235), choices=formats,
            style=wx.CB_READONLY)
        formats_cb.Bind(wx.EVT_COMBOBOX, self.on_select_fcb)

        st3 = wx.StaticText(panel, label='Size :',
                            style=wx.ALIGN_LEFT, pos=(163,235))
        st3.SetFont(font2)
        reso = ['300','250','200','150','100']
        reso_cb = wx.ComboBox(panel, pos=(197, 235), choices=reso,
                            style=wx.CB_READONLY)
        reso_cb.Bind(wx.EVT_COMBOBOX,self.on_select_rcb)

        st4 = wx.StaticText(panel, label='BG Color :',
                            style=wx.ALIGN_LEFT, pos=(250,235))

        st4.SetFont(font2)
        colors = ['Black', 'White', 'Blue','Red']
        bg_clr_cb = wx.ComboBox(panel, pos=(310, 235), choices=colors,
                            style=wx.CB_READONLY)
        bg_clr_cb.Bind(wx.EVT_COMBOBOX,self.on_select_clr)

        convert_btn = wx.Button(panel, label='Convert Files',pos=(270,280))
        convert_btn.Bind(wx.EVT_BUTTON,self.on_convert)

        font3 = wx.Font(7, wx.DEFAULT, wx.NORMAL, wx.DEFAULT)
        help_tag = wx.StaticText(panel, label='Help:hamzahamza050875@gmail.com',
                                style=wx.ALIGN_LEFT, pos=(3,297))
        help_tag.SetFont(font3)

        self.Show()

#============================================================================
    def update_dxf_listing(self, folder_path):
        global savePath 
        savePath="/".join(folder_path.split("\\"))
        savePath=savePath+'/image'
        self.current_folder_path = folder_path
        print(savePath)
        """
        this function look for the paths of the dxf file using glob
        the folder_path parm : for the folder
        """
        dxfs = glob.glob(folder_path + '/*.pdf')    #this search for the dxf in the folder_path
        if dxfs == [] :
             wx.MessageBox('No PDF files were found in this file', 'Not Found', wx.OK | wx.ICON_ERROR)
        names = []
        index = 0

        for dxf in dxfs:
            user_files.append(dxf)

        for name in user_files :
            self.list_ctrl.InsertItem(index, name)
            index += 1


    def Txt_Ent(self,event):
        user_folder1 = (str(self.rtb.GetValue()))
        self.update_dxf_listing(user_folder1)



    def on_open_folder(self, event):
        title = "Choose a directory:"
        dlg = wx.DirDialog(self, title,style=wx.DD_DEFAULT_STYLE)

        if dlg.ShowModal() == wx.ID_OK:
            user_folder2 = dlg.GetPath()
            self.update_dxf_listing(user_folder2)
        dlg.Destroy()


    def on_select_fcb(self, event):
        global formats
        formats = event.GetString()

    def on_select_rcb(self,event):
        global reso
        reso = event.GetString()
        reso = int(reso)

    def on_select_clr(self, event):
        clr_name = event.GetString()
        global color
        if clr_name == 'Black':
            color = '#000000'
        elif clr_name == 'White':
            color = '#FFFFFF'
        elif clr_name == 'Blue':
            color = '#0000FF'
        else : color = '#FF0000'


    def on_convert(self, event):
        # first =  DXF2IMG()
        # if formats and reso and color:
        # first.convert_dxf2img(user_files, img_format=formats, img_res=reso,clr=color )
        pdfPath = 'C:/Users/Boqibin1/Desktop/dxf/ilovepdf_merged (5).pdf'
        imagePath = 'C:/Users/Boqibin1/Desktop/dxf/image'
        first =  PDF2IMAGE()
        print(savePath)
        # first.pyMuPDF_fitz(user_files,savefolder=savePath)#只是转换图片
        first.pyMuPDF2_fitz(user_files,savefolder=str(savePath),saveFirstName='',saveName='_image',offsetLen=4000)#只是转换图片
        first.pyMuPDF2_fitz(user_files,savefolder=str(savePath),saveFirstName='L_',saveName='_image',offsetLen=9000)#只是转换图片
        # else :
        #      wx.MessageBox( 'Please select all things',
        #                     'Not selected', wx.OK | wx.ICON_ERROR)


if __name__ == '__main__':
    app = wx.App()
    frame = The_GUI()
    app.MainLoop()
```

