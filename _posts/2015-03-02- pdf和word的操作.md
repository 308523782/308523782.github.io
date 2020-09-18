---
layout: post
title: pdf和word的操作
date: 2020-9-10
categories: blog
tags: [pdf和word的操作]
description: 我们选择用asposel来处理word合并，word转PDF。使用itext相关组件来生成，合并pdf，为pdf生成页码，以及html转pdf。 在选择的过程中也考虑过使用docx4j和itext来进行word的相关操作，但是测试效果并不理想，docx4j和itext对于环境字体的要求比较高，操作起来复杂，转成的pdf效果不太好，而docx4j还无法对doc进行操作，所以我们采用了现有的方案。
---

### pdf和word的操作
##### 1. 背景
> 最近在上会系统中用到了很多pdf、word、html的相关的操作，所以在这里做一下总结。


##### 2.  处理方案
> &nbsp;&nbsp;&nbsp;&nbsp;我们选择用asposel来处理word合并，word转PDF。使用itext相关组件来生成，合并pdf，为pdf生成页码，以及html转pdf。 在选择的过程中也考虑过使用docx4j和itext来进行word的相关操作，但是测试效果并不理想，docx4j和itext对于环境字体的要求比较高，操作起来复杂，转成的pdf效果不太好，而docx4j还无法对doc进行操作，所以我们采用了现有的方案。

##### 3.  引入相关的jar

###### &nbsp;&nbsp;3.1由于是破解版，在互联网仓库无法下载，需手动 将jar包引入本地库

mvn install:install-file -DgroupId=com.aspose -DartifactId=aspose.words -Dversion=18.6 -Dpackaging=jar -Dfile=aspose.words-18.6.jar

###### &nbsp;&nbsp;3.2引入aspose
```html
<dependency>
    <groupId>com.aspose</groupId>
    <artifactId>aspose.words</artifactId>
    <version>18.6</version>
</dependency>
```
> &nbsp;&nbsp;&nbsp;&nbsp;aspose的引入比较简单，但是这个jar在Maven的中央仓库无法下载，我们已经把这个jar放到公司的中央仓库上了，可以直接引用。
###### &nbsp;&nbsp;3.3引入itext相关
```html
<!-- itext的核心依赖 -->
<dependency>
    <groupId>com.itextpdf</groupId>
    <artifactId>itextpdf</artifactId>
    <version>5.4.3</version>
</dependency>
<!-- itext的pdf中文处理相关依赖 -->
<dependency>
    <groupId>com.itextpdf</groupId>
    <artifactId>itext-asian</artifactId>
    <version>5.2.0</version>
</dependency>
<!-- itext的字体依赖 -->
<dependency>
    <groupId>com.lowagie</groupId>
    <artifactId>itext</artifactId>
    <version>2.0.8</version>
</dependency>
<!-- itext的html转pdf的依赖 -->
<dependency>
    <groupId>org.xhtmlrenderer</groupId>
    <artifactId>flying-saucer-pdf-itext5</artifactId>
    <version>9.1.5</version>
</dependency>
```
> &nbsp;&nbsp;&nbsp;&nbsp;itext的依赖在Maven的中央仓库上都可以找到，唯一需要注意的是html转pdf的依赖，这个依赖本身是不支持在html转pdf的时候中文自动换行的，所以这个依赖需要引用公司的仓库里面的jar，如果引用中央仓库上的，会在html转pdf的过程中，一行中文过长，但是无法自动换行，导致超出的文字无法显示的问题。

> &nbsp;&nbsp;&nbsp;&nbsp;需要引入的依赖就是这些，下面开始介绍如何具体的操作。

##### 4.  代码演示
###### &nbsp;&nbsp;4.1 aspose合并word
```java
/**
  * aspose的方式多文档合并,合并的文档格式可以是doc和docx（包括两种格式的混合）,
  * 根据传入生成文件名的后缀产生doc,docx,pdf不同格式的文件
  * @param fileList 需要合并的文件名集合
  * @param finalFile 生成的文件名
  */
public static void docMerge(List<String> fileList,String finalFile) {
  Document newFile = null;
  Document doc = null;
    try {
        if (!PdfTool.getLicense()) {
        throw new Exception("com.aspose.words lic ERROR!");
        }
        if(null != fileList && fileList.size()>0) {
          newFile = new Document();
          newFile.removeAllChildren();
          for(int i = 0; i < fileList.size(); i++) {
              doc = new Document(fileList.get(i));
              newFile.appendDocument(doc, ImportFormatMode.USE_DESTINATION_STYLES);
          }
          String suffix=finalFile.substring(finalFile.lastIndexOf(".")+1);
          if (suffix .equals("docx")) {
            newFile.save(finalFile, SaveFormat.DOCX);
          } else if(suffix .equals("doc")) {
            newFile.save(finalFile, SaveFormat.DOC);
          } else if(suffix .equals("pdf")) {
            newFile.save(finalFile, SaveFormat.PDF);
          }else {
              throw new Exception("未知输出文件格式");
          }
        }
      } catch (Exception e) {
          e.printStackTrace();
      }
}
```
> &nbsp;&nbsp;&nbsp;&nbsp;可以看到aspose的操作十分简单，使用起来特别方便，甚至可以直接在合并word的时候，直接输出一个pdf文件。主要的是aspose将word中节点都读取到了，然后再以指定的格式输出。输出格式也是支持很多，有HTML、JPEG、GIF、BMP、PNG、DOC、DOCX等常见的格式，还有一些不常见的格式我就不一一列举 ，有兴趣可以去看看SaveFormat里面具体有哪些格式。
> &nbsp;&nbsp;&nbsp;&nbsp;需要注意的是，在使用aspose的时候，需要验证一下许可证，就是上面代码中的PdfTool.getLicense()方法。
```java
/**
  * @author blue
  * @Description aspose的方式word转PDF所需要的许可证验证
  * @return
  * @throws Exception
  */
public static boolean getLicense() throws Exception {
  boolean result = false;
  try {
    InputStream is = com.aspose.words.Document.class
        .getResourceAsStream("/com.aspose.words.lic_2999.xml");
    License aposeLic = new License();
    aposeLic.setLicense(is);
    result = true;
    is.close();
  } catch (Exception e) {
    e.printStackTrace();
    throw e;
  }
  return result;
}
```
> 这个方法只需要像上面代码中调用一下就行。
###### &nbsp;&nbsp;4.2 aspose word转pdf
```java
/**
  * @author blue
  * @Description aspose方式的word转PDF,需要注意word里面的一些特殊符号可能不支持
  * @param inPath word文件地址
  * @param outPath PDF文件地址
  */
public static void doc2pdf(String inPath, String outPath) {
      try {
        if (!getLicense()) {
        throw new Exception("com.aspose.words lic ERROR!");
      }
          File file = new File(outPath);
          FileOutputStream os = new FileOutputStream(file);
          Document doc = new Document(inPath);
          doc.save(os, SaveFormat.PDF);// 全面支持DOC, DOCX, OOXML, RTF HTML, OpenDocument, PDF,EPUB, XPS, SWF 相互转换
      } catch (Exception e) {
          e.printStackTrace();
      }
  }
```
> &nbsp;&nbsp;&nbsp;&nbsp;读取一个word文件中的节点，保存为一个pdf文件。注意验证许可证。
###### &nbsp;&nbsp;4.3 itext用代码生成一个pdf
```java
public void CreatPDF() {
    //创建文件
    Document document = new Document();
    //建立一个书写器
    PdfWriter writer = PdfWriter.getInstance(document, new FileOutputStream("C:/Users/Desktop/testPdf.pdf"));
    //打开文件
    document.open();
    //中文字体,解决中文不能显示问题
    BaseFont bfChinese = BaseFont.createFont("STSong-Light","UniGB-UCS2-H",BaseFont.NOT_EMBEDDED);
    //蓝色字体
    Font blueFont = new Font(bfChinese);
    blueFont.setColor(BaseColor.BLUE);
    //段落文本
    Paragraph paragraphBlue = new Paragraph("paragraphOne blue front", blueFont);
    document.add(paragraphBlue);
    //绿色字体
    Font greenFont = new Font(bfChinese);
    greenFont.setColor(BaseColor.GREEN);
    //创建章节
    Paragraph chapterTitle = new Paragraph("段落标题xxxx", greenFont);
    Chapter chapter = new Chapter(chapterTitle, 1);
    chapter.setNumberDepth(0);
    Paragraph sectionTitle = new Paragraph("部分标题", greenFont);
    Section section = chapter.addSection(sectionTitle);
    Paragraph sectionContent = new Paragraph("部分内容", blueFont);
    section.add(sectionContent);
    //将章节添加到文章中
    document.add(chapter);
    //关闭文档
    document.close();
    //关闭书写器
    writer.close();
  }
```
> &nbsp;&nbsp;&nbsp;&nbsp;以上就是创建一个简单的pdf文件的步骤。其核心就是创建文档，创建书写器，打开文档，往文档中添加内容，关闭文档，关闭书写器。pdf内置很多属性，例如标题，正文，字体颜色，插入图片，创建一个表格，但是大体上的步骤还是一样的。只是创建一些特定的样式，步骤少许不同，这里就不一一列举了。有一个比较重要的点就是中文字体。
```java
BaseFont bfChinese = BaseFont.createFont("STSong-Light","UniGB-UCS2-H",BaseFont.NOT_EMBEDDED);
```
> &nbsp;&nbsp;&nbsp;&nbsp;需要像这样设置一下，才能支持中文字体。
> &nbsp;&nbsp;&nbsp;&nbsp;创建pdf文档的时候，还能设置文档的大小，旋转，上下左右的边距等属性。具体的可以去看Document的构造方法。
```java
//自定义纸张
//Rectangle rectPage = new Rectangle(350, 620);
// 定义A4页面大小
Rectangle rectPage = new Rectangle(PageSize.A4);
// 横向的pdf
rectPage = rectPage.rotate();
Document document = new Document(rectPageSize,left,right,top,bottom);
```
> &nbsp;&nbsp;&nbsp;&nbsp;这样就创建了一个拥有上下左右边距的横向A4纸张大小的pdf。
###### &nbsp;&nbsp;4.4 itext合并pdf
```java
/**
  * @author blue
  * @Description itextpdf合并pdf
  * @param fileList 需要合并的pdf
  * @param savepath 合并后pdf路径
  */
public static boolean morePdfTopdf(List<String> fileList, String savepath) {
  com.itextpdf.text.Document document = null;
    try {
      FileUtils.fileExists(savepath);
      PdfReader nReader = new PdfReader(fileList.get(0));
        document = new com.itextpdf.text.Document(nReader.getPageSize(1));
        PdfCopy copy = new PdfCopy(document, new FileOutputStream(savepath));
        document.open();
        for (int i = 0; i < fileList.size(); i++) {
            PdfReader reader = new PdfReader(fileList.get(i));
            int n = reader.getNumberOfPages();
            for (int j = 1; j <= n; j++) {
                document.newPage();
                PdfImportedPage page = copy.getImportedPage(reader, j);
                copy.addPage(page);
            }
            reader.close();
        }
        nReader.close();
    } catch (IOException e) {
        e.printStackTrace();
        return false;
    } catch (DocumentException e) {
        e.printStackTrace();
        return false;
    } finally {
        if (document != null) {
            document.close();
        }
    }
    return true;
}
```
> &nbsp;&nbsp;&nbsp;&nbsp;上面就是itext合并pdf的方法，其思路是将需要合并pdf一页一页的读取其中的内容，然后复制到一个新的pdf上，核心对象是PdfCopy对象，这个类是itetx主要用来做pdf的复制和合并的。
> &nbsp;&nbsp;&nbsp;&nbsp;操作pdf的流用完之后，一定要关闭，不要合并之后的pdf一直被占用，会影响之后进行的一些操作。切记！
###### &nbsp;&nbsp;4.5 itext为pdf生成页码
> &nbsp;&nbsp;&nbsp;&nbsp;在创建pdf的时候，生成页码比较容易，直接用itext自带的监听事件去处理比较方便。但是在上会系统中，更多的需求是需要我们在已有的pdf文件上生成页码，所以这里我讲一下itext在已有的pdf上生成页码。
```java
/**
  * @author blue
  * @Description 为已有的pdf生成页码
  * @param sourcePdf 需要生成页码的pdf路径
  * @param targetPdf 生成页码的pdf路径
  */
public static void pdfAddPageNum(String sourcePdf, String targetPdf){
  try {
    BufferedOutputStream buffer = new BufferedOutputStream(new FileOutputStream(new java.io.File(targetPdf)));
    PdfReader reader = new PdfReader(sourcePdf);//打开目标pdf
    PdfStamper stamper = new PdfStamper(reader, buffer);//PdfStamper打开reader，并且设置pdf
    BaseFont baseFont = BaseFont.createFont("STSong-Light,Bold","UniGB-UCS2-H",BaseFont.NOT_EMBEDDED);
    PdfContentByte content;
    for (int i = 1; i < reader.getNumberOfPages()+1; i++) {
        content = stamper.getOverContent(i);
        content.setFontAndSize(baseFont, 9);
        content.setColorFill(BaseColor.BLACK);
        content.setTextMatrix(reader.getPageSize(i).getHeight()/2, 20);//定义页码位置 pdf横向
        String centerMark=String.valueOf("- "+i+" -");
        content.showText(centerMark);
    }
    stamper.close();
    reader.close();
  } catch (Exception e) {
    e.printStackTrace();
  }
}
```
> &nbsp;&nbsp;&nbsp;&nbsp;主要是用由PdfStamper这个类去操作pdf生成页码的，这个类是由itext提供的，主要作用于产生页眉，页脚，页码，水印等功能。处理思路也比较简单，将pdf文件一页一页的读取，然后为每一页添加对应的页码。
> &nbsp;&nbsp;&nbsp;&nbsp;需要注意的是页码位置的计算。因为pdf无论是横向还是纵向，它的宽高是不变的。例如纵向的A4纸张，我们加页码只需要取短的那一边的一半的长度去设置页码就行了，如果是横向的A4纸张，那么我们需要取长的那一边的长度去设置页码了，但是不管是横向还是纵向的A4纸张，pdf的宽永远是短的那一边，高永远是长的那一边。这时候我们去生成页码的时候需要去判断这个pdf是横向还是纵向的，然后去判断获取宽还是高。
```java
PdfNumber rotate = page.getAsNumber(PdfName.ROTATE);
if(rotate == null || rotate.intValue()%180 ==0) {
  content.setTextMatrix(reader.getPageSize(i).getWidth()/2, 20);//定义页码位置  pdf纵向
}else {
  content.setTextMatrix(reader.getPageSize(i).getHeight()/2, 20);//定义页码位置 pdf横向
}
```
> &nbsp;&nbsp;&nbsp;&nbsp;我们可以通过上面的代码去判断pdf是横向还是纵向的，然后去设置页码的不同位置
###### &nbsp;&nbsp;4.6 itext html转pdf
```java
/**
  * html转pdf
  * @param html html文档
  * @param fileName 文件名称
  */
public static void html2pdf(String html,String path){
  OutputStream os = null;
  try {
    File file = new File(path);
    os = new FileOutputStream(file);
    ITextRenderer renderer = new ITextRenderer();
    renderer.setDocumentFromString(html);
    ITextFontResolver fontResolver = renderer.getFontResolver();
    Resource fontPath = ContextUtils.getContext().getResource("WEB-INF/font");
    fontResolver.addFont(fontPath.getFile().getPath() + File.separator + "simsun.ttc",BaseFont.IDENTITY_H, BaseFont.NOT_EMBEDDED);
    renderer.layout();
    renderer.createPDF(os);
    os.flush();
    os.close();
  }catch (Exception e) {
    e.printStackTrace();
    throw new BusinessException("html文件生成pdf出错，错误原因：" + e.getMessage());
  }finally{
    if(os != null){
      try {
        os.close();
      } catch (IOException e) {
        e.printStackTrace();
        throw new BusinessException("文件流处理出错，错误原因：" + e.getMessage());
      }
    }
  }
}
```
> &nbsp;&nbsp;&nbsp;&nbsp;html转pdf的形式有很多，ITextRenderer的对象可以读取一个html文件，或者一个html的字符串，甚至是一个html的网址。但是唯一的问题是，被转的html的格式是非常严格的，所有的标签必须是闭合的。

###### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;稍微提一句，对于web页面加载pdf，可以使用pdf.js去加载，加载效果还不错，唯一缺陷是对于ie的支持，最低需要ie9才能显示。pdf.js官方提供了一个加载pdf的web项目，我们在使用pdf.js时候，如果没有特别的样式定制，可以直接在我们的web项目中引入官方提供的项目去加载pdf。这里主要讲的是pdf的一些转换操作，页面加载pdf就不在这里详细的说了。

以上就是java对pdf和word的一些操作。



















