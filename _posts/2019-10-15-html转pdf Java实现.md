### HTML转PDF Java实现

#### 1.首先引入maven依赖

```Java
<dependency>
            <groupId>com.itextpdf</groupId>
            <artifactId>html2pdf</artifactId>
            <version>2.0.2</version>
 </dependency>
```

#### 2.加入中文字体

SimSun.ttf(自己去网上下载吧)

#### 3.废话不说，直接贴代码

```Java
import com.itextpdf.html2pdf.ConverterProperties;
import com.itextpdf.html2pdf.HtmlConverter;
import com.itextpdf.html2pdf.resolver.font.DefaultFontProvider;
import com.itextpdf.kernel.pdf.PdfDocument;
import com.itextpdf.kernel.pdf.PdfWriter;
import com.itextpdf.layout.Document;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;
@Slf4j
@Component
public class HtmlToPdf {
	public static final String FONT = "/font/SimSun.ttf";

	public Map<String, Object> html2Pdf(String htmlStr) throws Exception {
		Map<String, Object> map = new HashMap<>();
		ByteArrayInputStream inputStream = null;
		byte[] buffer;
		try {
			ConverterProperties props = new ConverterProperties();
			DefaultFontProvider defaultFontProvider = new DefaultFontProvider(false, false, false);
			defaultFontProvider.addFont(FONT);
			props.setFontProvider(defaultFontProvider);
			ByteArrayOutputStream out = new ByteArrayOutputStream();
			PdfWriter writer = new PdfWriter(out);
			PdfDocument pdf = new PdfDocument(writer);
			Document document = HtmlConverter.convertToDocument(new ByteArrayInputStream(htmlStr.getBytes()), pdf, props);
			int pages = pdf.getNumberOfPages();
			document.getRenderer().close();
			document.close();
			pdf.close();
			inputStream = new ByteArrayInputStream(out.toByteArray());
			buffer = new byte[inputStream.available()];
			inputStream.read(buffer);
			map.put("pages", pages);
			map.put("byteFile", buffer);
			return map;
		}catch (Exception e){
			inputStream.close();
			log.error("html转pdf失败: "+ e.getMessage());
			throw new Exception("html转pdf失败");
		}
	}

	public static void main(String[] args) {
		try {
			StringBuffer buffer = new StringBuffer();
			InputStream is = new FileInputStream("F:\\C201910102873.txt");
			String line; // 用来保存每行读取的内容
			BufferedReader reader = new BufferedReader(new InputStreamReader(is));
			line = reader.readLine(); // 读取第一行
			while (line != null) { // 如果 line 为空说明读完了
				buffer.append(line); // 将读到的内容添加到 buffer 中
				buffer.append("\n"); // 添加换行符
				line = reader.readLine(); // 读取下一行
			}
			reader.close();
			is.close();
			HtmlToPdf html = new HtmlToPdf();
			html.html2Pdf(buffer.toString());
			FileOutputStream outputStream = new FileOutputStream(new File("F:\\test.pdf"));
			Map<String, Object> map = html.html2Pdf(buffer.toString());
			byte[] files = (byte[])map.get("byteFile");
			outputStream.write(files);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

}
```

#### 4.总结

根据踩坑多次才总结出来的