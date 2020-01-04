```java
		/**
     * 将base64解码成图片并保存在传入的路径下
     * 第一个参数为base64 ，第二个参数为路径
     * @param base64, imgFilePath
     * @return boolean
     */
    public boolean Base64ToImage(String base64, String imgFilePath) {
        // 对字节数组字符串进行Base64解码并生成图片
        if (base64 == null) // 图像数据为空
            return false;
        BASE64Decoder decoder = new BASE64Decoder();
        try {
            // Base64解码
            byte[] b = decoder.decodeBuffer(base64);
            for (int i = 0; i < b.length; ++i) {
                if (b[i] < 0) {// 调整异常数据
                    b[i] += 256;
                }
            }
            OutputStream out = new FileOutputStream(imgFilePath);
            out.write(b);
            out.flush();
            out.close();
            return true;
        } catch (Exception e) {
            return false;
        }
    }
```



```java
		/**
     * 根据班级名称导出考生证件照
     * @return
     */
    @Get("/generate_user_picture")
    public String generateUserPicture(){
        List<Integer> departmentList = new ArrayList<>();
        departmentList.add(15);
        departmentList.add(18);
        departmentList.add(12);
        departmentList.add(21);
        departmentList.add(22);

        int userPicNum = 0;

        for (Integer depId : departmentList) {
            String departmentName = departmentTreeBiz.getNameById(depId);
            String dir = "/Users/suxiongwei/Desktop" + File.separator + departmentName + File.separator;
            String fileName,filePath;

            List<Integer> userIds = departmentUserBiz.getUserIdsByDepId(depId);
            List<UsersModel> usersModels = userBiz.getUserByIds(userIds);

            for (UsersModel usersModel : usersModels) {
                if(StringUtils.isEmpty(usersModel.getPhoto()) || usersModel.getPhoto().startsWith("http")){
                    LOGGER.info("{}没有证件照，导出跳过" , usersModel.getId());
                    continue;
                }
                File dirFile = new File(dir);
                if (!dirFile.exists()) {
                    dirFile.mkdirs();
                }
                fileName = usersModel.getUserName().replaceAll("@1","") + "-" + usersModel.getSurname() + ".jpg";
                filePath = dir + fileName;

                Bson filter = Filters.eq("_id", new ObjectId(usersModel.getPhoto()));
                Document document = imageByteMongoBiz.findOne(filter);
                String encodeStr = document.getString("imageEncodeStr");
                examInfoBiz.Base64ToImage(encodeStr,filePath);
                userPicNum++;
            }
        }
        return "@" + JSONUtil.writeValueAsString(userPicNum);
    }
```

