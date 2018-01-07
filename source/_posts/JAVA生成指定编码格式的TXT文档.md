---
title: JAVA生成指定编码格式的TXT文档
date: 2017-12-22 00:23:18
tags:
---
用技术去生存，用艺术去生活。——《死亡诗社》
 <!-- more -->
 ###    朝花
在进行心镜预约文档对接的时候，对方的编码方式是GB2312，接收的是ANSI编码的TXT文档，而工程却是生成的是以UTF-8编码的TXT文档，在使用PrintWriter类进行多次尝试后，仍未解决该问题，依旧是生成UTF-8的文档。在进行咨询后，决定使用OutputStreamWriter 输出流进行文档生成。对其编码“GB2312”后成功生成无乱码的ANSI格式的TXT文档

```bash
    public void RegRecentlyListTask(){
        PrintWriter pfp = null;
        try {
            List<String> work = registrationService.getWorkDate(CommUtil.formatShortDate(
                    CommUtil.getDayAfterNum(new Date(), 1)),MagicNum.MAX_NUM_WORK);
            String pathYear = HeartMirrorParameter.TXT_ROUTE;
            File fp = new File(pathYear);
            if (!fp.getParentFile().exists()){
                fp.getParentFile().mkdirs();
            }
            String str = registrationService.getRegistrationRecordList("2017-12-20");
            pfp= new PrintWriter(pathYear);
            pfp.print(str);
            logger.info("获取待就诊列表，生成txt文件");
        } catch (Exception e){
            e.printStackTrace();
            logger.error(e);
        } finally {
            pfp.close();
        }
    }
```
修改前

```bash
    public void RegRecentlyListTask(){
        try {
            List<String> work = registrationService.getWorkDate(CommUtil.formatShortDate(
                    CommUtil.getDayAfterNum(new Date(), 1)),MagicNum.MAX_NUM_WORK);
            String pathYear = HeartMirrorParameter.TXT_ROUTE;
            File fp = new File(pathYear);
            if (!fp.getParentFile().exists()){
                fp.getParentFile().mkdirs();
            }
            FileOutputStream fileOutputStream = new FileOutputStream(HeartMirrorParameter.TXT_ROUTE);
            OutputStreamWriter oStreamWriter = new OutputStreamWriter((fileOutputStream, "GB2312");
            oStreamWriter.write(registrationService.getRegistrationRecordList(work.get(0)));
            oStreamWriter.close();
            logger.info("获取待就诊列表，生成txt文件");
        } catch (Exception e){
            e.printStackTrace();
            logger.error(e);
        }
    }
```
修改后

### 夕拾
PrintWriter类已经很强大了，但其仍有自己的不足之处。
OutputStreamWriter虽然不如PrintWriter新但是仍能在某些时候起到PrintWriter代替不了的作用。
定义输出TXT文件格式：
```bash
//"UTF-8"
OutputStreamWriter oStreamWriter = new OutputStreamWriter((fileOutputStream, "UTF-8");
//"ANSI"
OutputStreamWriter oStreamWriter = new OutputStreamWriter((fileOutputStream, "GB2312");
```
