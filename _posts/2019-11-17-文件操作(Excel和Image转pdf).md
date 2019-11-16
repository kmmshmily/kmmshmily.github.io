### 文件操作(Excel和Image转pdf)

#### 1.excel转pdf

- 加入jar包(aspose-cells-8.5.2.jar)
- 直接贴代码

```Java
/**
 * excel转pdf
 *
 * @param in :
 * @return byte[]
 * @throws
 * @Author kmmshmily
 * @Date 2019/11/14 16:30
 */
public static byte[] excel2pdf(InputStream in) throws Exception {
    if (!getLicense()) { // 验证License 若不验证则转化出的pdf文档会有水印产生
        return null;
    }
    byte[] buffer = new byte[in.available()];
    try {
        long old = System.currentTimeMillis();
        Workbook wb = new Workbook(in);// 原始excel路径

        PdfSaveOptions pdfSaveOptions = new PdfSaveOptions();
        pdfSaveOptions.setOnePagePerSheet(true);//把内容放在一张PDF 页面上;
        ByteArrayOutputStream out = new ByteArrayOutputStream();
        wb.save(out, pdfSaveOptions);
        ByteArrayInputStream inputStream = new ByteArrayInputStream(out.toByteArray());
        buffer = new byte[inputStream.available()];
        inputStream.read(buffer);
        out.close();
        long now = System.currentTimeMillis();
        System.out.println("共耗时：" + ((now - old) / 1000.0) + "秒"); //转化用时
    } catch (Exception e) {
        e.printStackTrace();
    }
    return buffer;
}


    /**
     * 获取凭证
     *
     * @return boolean
     * @throws
     * @Author kmmshmily
     * @Date 2019/11/16 15:12
     */
    private static boolean getLicense() {
        boolean result = false;
        try {
            InputStream is =                                                FileConvertUtils.class.getClassLoader().getResourceAsStream("license.xml");
//            InputStream is = new FileInputStream(new File("/license.xml"));
            License aposeLic = new License();
            aposeLic.setLicense(is);
            result = true;
        } catch (Exception e) {
            log.error("获取凭证失败：" + e);
        }
        return result;
    }
```
- license.xml

```xml
       <License>
    <Data>
        <Products>
            <Product>Aspose.Total for Java</Product>
            <Product>Aspose.Words for Java</Product>
        </Products>
        <EditionType>Enterprise</EditionType>
        <SubscriptionExpiry>20991231</SubscriptionExpiry>
        <LicenseExpiry>20991231</LicenseExpiry>
        <SerialNumber>8bfe198c-7f0c-4ef8-8ff0-acc3237bf0d7</SerialNumber>
    </Data>
    <Signature>sNLLKGMUdF0r8O1kKilWAGdgfs2BvJb/2Xp8p5iuDVfZXmhppo+d0Ran1P9TKdjV4ABwAgKXxJ3jcQTqE/2IRfqwnPf8itN8aFZlV3TJPYeD3yWE7IT55Gz6EijUpC7aKeoohTb4w2fpox58wWoF3SNp6sK6jDfiAUGEHYJ9pjU=</Signature>
</License>

```

#### 2. Image转PDF

- 引入maven依赖

```Java
        <dependency>
            <groupId>com.lowagie</groupId>
            <artifactId>itext</artifactId>
            <version>2.1.7</version>
        </dependency>
```

- 直接贴代码

```Java
/**
 * 图片转pdf
 *
 * @param file :
 * @return byte[]
 * @throws
 * @Author kmmshmily
 * @Date 2019/11/14 16:39
 */

public static byte[] imageToPdf(byte[] file) throws Exception {

    String TAG = "PdfManager";
    Document doc = new Document(PageSize.A4, 20, 20, 20, 20);
    ByteArrayOutputStream out = new ByteArrayOutputStream();
    try {
        PdfWriter.getInstance(doc, out);
        doc.open();
        doc.newPage();

        Image png1 = Image.getInstance(file);
        float heigth = png1.getHeight();
        float width = png1.getWidth();
        int percent = Math.round(530 / width * 100);
        png1.setAlignment(Image.MIDDLE);
        png1.scalePercent(percent + 3);// 表示是原来图像的比例;
        doc.add(png1);
    } catch (Exception e) {
        log.error("图片转pdf失败：" + e);
        throw e;
    } finally {
        doc.close();
    }
    return out.toByteArray();
}
```
