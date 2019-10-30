### 文件上传

#### 后端上传，通过ip:port直接上传，间接组织http报文

直接贴代码：

- 客户端

  ```java
      //file为Multifile, url = ip:port
      log.info("获取文件流, fileName = " + file.getOriginalFilename() + "id :" + contractId);
          String paperContractUrl = "";
          CloseableHttpClient httpclient = HttpClients.createDefault();
          CloseableHttpResponse response = null;
      try {
          InputStream inputStream = file.getInputStream();
          byte[] buffer = new byte[inputStream.available()];
          inputStream.read(buffer);
          log.info("上传文件地址:" + url + "/file/uploadByMultipartFile");
          HttpPost httppost = new HttpPost(url + "/file/uploadByMultipartFile");
          RequestConfig defaultRequestConfig = RequestConfig.custom().setConnectTimeout(Integer.valueOf("50000")).setConnectionRequestTimeout(Integer.valueOf("5000"))
                  .setSocketTimeout(Integer.valueOf("150000")).build();
          httppost.setConfig(defaultRequestConfig);
          MultipartEntityBuilder multipartEntityBuilder = MultipartEntityBuilder.create();
          multipartEntityBuilder.setContentType(ContentType.MULTIPART_FORM_DATA);
          multipartEntityBuilder.addBinaryBody("file", buffer, ContentType.DEFAULT_BINARY, file.getOriginalFilename());
          HttpEntity reqEntity = multipartEntityBuilder.build();
          httppost.setEntity(reqEntity);
          response = httpclient.execute(httppost);
          if (response != null) {
              HttpEntity entity = response.getEntity();
              if (entity != null) {
                  paperContractUrl = EntityUtils.toString(entity, Charset.forName("UTF-8"));
              }
          }
  ```

- 服务端

      @RequestMapping(value = "/file/uploadByMultipartFile", method = RequestMethod.POST)
      public String uploadByMultipartFile(@RequestPart("file") MultipartFile file) {
          String urlPaperUrl = "";
          try {
       //            MultipartHttpServletRequest multipartRequest = 
       //            (MultipartHttpServletRequest) request;
      //            List<MultipartFile> files = multipartRequest.getFiles("file");
      //            MultipartFile file = files.get(0);
                  String fileName = file.getOriginalFilename();
                  InputStream inputStream = file.getInputStream();
                  byte[] buffer = new byte[inputStream.available()];
                  inputStream.read(buffer);
                  if (StringUtils.isBlank(fileName)) {
                      throw new Exception("文件名称不能为空");
                  }
                  // urlPaperUrl = "此处省略与dfs交互部分代码";
                  return urlPaperUrl;
              } catch (Exception e) {
                  logger.error("上传文件失败 :" + e);
                  return "";
              }
          }
  
- 小结

上述代码已贴完毕，服务端返回的是String 类型，  所以客户端直接解析即可；如果服务端返回的是json，则客户端解析出来的字符串就是服务端返回的jaon字符串。