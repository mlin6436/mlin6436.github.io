---
layout: post
title: "Add Watermark to PDFs using iTextSharp"
tags: ["watermark", "itextsharp", "c#", ".net", "programmatically", "pdf"]
---

<div class="message">
Being able to add text or image watermark to PDF files is one of those handy things you can archive via program.
</div>

The only library required for the little program is [iTextSharp](https://www.nuget.org/packages/itextsharp/). For those who haven't used it yet, iTextSharp is a C# version of [iText](http://itextpdf.com/), which allows you to generate or manipulate PDFs.

So let's cut the chitchat, and go straight to the code. By calling 'AddTextWatermark' or 'AddImageWatermark' function with required parameters supplied, you are ready to rock!

{% highlight csharp %}
public void CreateTemplate(string watermarkText, string targetFileName){
    var document = new Document())
    var pdfWriter = PdfWriter.GetInstance(document, new FileStream(targetFileName, FileMode.Create));
    var font = new Font(Font.FontFamily.HELVETICA, 60, Font.NORMAL, BaseColor.LIGHT_GRAY);
    document.Open();
    ColumnText.ShowTextAligned(pdfWriter.DirectContent, Element.ALIGN_CENTER, new Phrase(watermarkText, font), 300, 400, 45);
    document.Close();}public void AddTextWatermark(string sourceFilePath, string watermarkTemplatePath, string targetFilePath){
    var pdfReaderSource = new PdfReader(sourceFilePath);
    var pdfStamper = new PdfStamper(pdfReaderSource, new FileStream(targetFilePath, FileMode.Create));
    var pdfReaderTemplate = new PdfReader(watermarkTemplatePath);
    var page = pdfStamper.GetImportedPage(pdfReaderTemplate, 1);

    for (var i = 0; i < pdfReaderSource.NumberOfPages; i++)
    {
        var content = pdfStamper.GetUnderContent(i + 1);
        content.AddTemplate(page, 0, 0);
    }

    pdfStamper.Close();
    pdfReaderTemplate.Close();}public void AddImageWatermark(string sourceFilePath, string watermarkImagePath, string targetFilePath){
    var pdfReader = new PdfReader(sourceFilePath);
    var pdfStamper = new PdfStamper(pdfReader, new FileStream(targetFilePath, FileMode.Create));
    var image = Image.GetInstance(watermarkImagePath);
    image.SetAbsolutePosition(200, 400);

    for (var i = 0; i < pdfReader.NumberOfPages; i++)
    {
        var content = pdfStamper.GetUnderContent(i + 1);
        content.AddImage(image);
    }

    pdfStamper.Close();
}
{% endhighlight %}
