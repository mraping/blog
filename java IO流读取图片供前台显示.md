## 需求
上传的图片以bytes的形式保存在mongo中，因此涉及到：
- 如何存储
- 如何下载
- 如何以图片的形式在浏览器显示

## 代码
### 存储
```
// 文件大小
long fileSize = uploadFile.getSize();
// 文件名称
String fileName = "文件名称";
// 获取uploadFile的输入流
InputStream inputStream = uploadFile.getInputStream();
// 图片的包装流
BufferedImage bi = ImageIO.read(inputStream);
ByteArrayOutputStream bs = new ByteArrayOutputStream();
ImageIO.write(bi, fileExt.substring(1), bs);
byte[] bytes = bs.toByteArray();
String outputStr = encoder.encodeBuffer(bytes).trim();
// 保存到mongo
Map map = new HashMap(4);
map.put("imageEncodeStr", outputStr);
map.put("fileSize", fileSize);
map.put("fileName", fileName);
map.put("fileExt", fileExt);
map.put("companyId", companyId);
map.put("timestamp", System.currentTimeMillis());
Document document = imageByteMongoBiz.insertOne(map);
```

### 下载
```
Bson filter = Filters.eq("_id", new ObjectId(imageId));
Document document = imageByteMongoBiz.findOne(filter);
String encodeStr = document.getString("imageEncodeStr");
String fileExt = document.getString("fileExt");
String fileName = document.getString("fileName");
byte[] bytes = decoder.decodeBuffer(encodeStr);
LOGGER.info("---:{}", encodeStr);
LOGGER.info("---:bytes.length={}", bytes.length);

HttpServletResponse response = inv.getResponse();
response.setContentType("application/force-download");
response.addHeader("Content-Disposition", "attachment; filename=" + URLEncoder.encode(fileName, "UTF-8"));
response.setContentLength(bytes.length);

ByteArrayInputStream bais = new ByteArrayInputStream(bytes);
BufferedImage bi1 = ImageIO.read(bais);
OutputStream out = response.getOutputStream();
ImageIO.write(bi1, fileExt.substring(1), out);
```

> 打开为图片
```
// fileExt.substring(1) 为图片后缀 如jpg
response.setHeader("Content-Type", "image/"+fileExt.substring(1));
```