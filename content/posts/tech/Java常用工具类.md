---
title: "Java常用工具类"
date: 2022-09-05T00:17:58+08:00
lastmod: 2022-09-05T00:17:58+08:00
author: ["藏锋"]
keywords: 
- netty
- 网络编程
categories: 
- tech
tags: 
- netty
- tech
description: "一些常用的工具类，自用"
weight:
slug: ""
draft: false # 是否为草稿
comments: true
reward: true # 打赏
mermaid: true #是否开启mermaid
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示路径
cover:
    image: "" #图片路径例如：posts/tech/123/123.png
    caption: "" #图片底部描述
    alt: ""
    relative: false
---

# IP查询的一些方法

 IP_URL = "http://whois.pconline.com.cn/ipJson.jsp?ip=%s&json=true"
 ```java
/**  
 * 获取ip地址  
 */  
public static String getIp(HttpServletRequest request) {  
    String ip = request.getHeader("x-forwarded-for");  
    if (ip == null || ip.length() == 0 || UNKNOWN.equalsIgnoreCase(ip)) {  
        ip = request.getHeader("Proxy-Client-IP");  
    }  
    if (ip == null || ip.length() == 0 || UNKNOWN.equalsIgnoreCase(ip)) {  
        ip = request.getHeader("WL-Proxy-Client-IP");  
    }  
    if (ip == null || ip.length() == 0 || UNKNOWN.equalsIgnoreCase(ip)) {  
        ip = request.getRemoteAddr();  
    }  
    String comma = ",";  
    String localhost = "127.0.0.1";  
    if (ip.contains(comma)) {  
        ip = ip.split(",")[0];  
    }  
    if (localhost.equals(ip)) {  
        // 获取本机真正的ip地址  
        try {  
            ip = InetAddress.getLocalHost().getHostAddress();  
        } catch (UnknownHostException e) {  
            e.printStackTrace();  
        }  
    }  
    return ip;  
}
/**  
 * 根据ip获取详细地址  
 */  
public static String getCityInfo(String ip) {  
    String api = String.format(IP_URL, ip);  
    JSONObject object = JSONUtil.parseObj(HttpUtil.get(api));  
    return object.get("addr", String.class);  
}

/**  
 * 获得当天是周几  
 */  
public static String getWeekDay() {  
    String[] weekDays = {"Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"};  
    Calendar cal = Calendar.getInstance();  
    cal.setTime(new Date());  
  
    int w = cal.get(Calendar.DAY_OF_WEEK) - 1;  
    if (w < 0) {  
        w = 0;  
    }  
    return weekDays[w];  
}

public static String getBrowser(HttpServletRequest request) {  
    UserAgent userAgent = UserAgent.parseUserAgentString(request.getHeader("User-Agent"));  
    Browser browser = userAgent.getBrowser();  
    return browser.getName();  
}

/**  
 * 导出excel  
 */public static void downloadExcel(List<Map<String, Object>> list, HttpServletResponse response) throws IOException {  
    String tempPath = System.getProperty("java.io.tmpdir") + IdUtil.fastSimpleUUID() + ".xlsx";  
    File file = new File(tempPath);  
    BigExcelWriter writer = ExcelUtil.getBigWriter(file);  
    // 一次性写出内容，使用默认样式，强制输出标题  
    writer.write(list, true);  
    //response为HttpServletResponse对象  
    response.setContentType("application/vnd.openxmlformats-officedocument.spreadsheetml.sheet;charset=utf-8");  
    //test.xls是弹出下载对话框的文件名，不能为中文，中文请自行编码  
    response.setHeader("Content-Disposition", "attachment;filename=file.xlsx");  
    ServletOutputStream out = response.getOutputStream();  
    // 终止后删除临时文件  
    file.deleteOnExit();  
    writer.flush(out, true);  
    //此处记得关闭输出Servlet流  
    IoUtil.close(out);  
}

/**  
 * 将文件名解析成文件的上传路径  
 */  
public static File upload(MultipartFile file, String filePath) {    
    String suffix = getExtensionName(file.getOriginalFilename());  
    StringBuffer nowStr = fileRename();  
    try {  
        String fileName = nowStr + "." + suffix;  
        String path = filePath + fileName;  
        // getCanonicalFile 可解析正确各种路径  
        File dest = new File(path).getCanonicalFile();  
        // 检测是否存在目录  
        if (!dest.getParentFile().exists()) {  
            dest.getParentFile().mkdirs();  
        }  
        // 文件写入  
        file.transferTo(dest);  
        return dest;  
    } catch (Exception e) {  
        e.printStackTrace();  
    }  
    return null;  
}

/**  
 * MultipartFile转File  
 */public static File toFile(MultipartFile multipartFile) {  
    // 获取文件名  
    String fileName = multipartFile.getOriginalFilename();  
    // 获取文件后缀  
    String prefix = "." + getExtensionName(fileName);  
    File file = null;  
    try {  
        // 用uuid作为文件名，防止生成的临时文件重复  
        file = File.createTempFile(IdUtil.simpleUUID(), prefix);  
        // MultipartFile to File  
        multipartFile.transferTo(file);  
    } catch (IOException e) {  
        e.printStackTrace();  
    }  
    return file;  
}


  
/**  
 * 获取文件扩展名，不带 .  
 */public static String getExtensionName(String filename) {  
    if ((filename != null) && (filename.length() > 0)) {  
        int dot = filename.lastIndexOf('.');  
        if ((dot > -1) && (dot < (filename.length() - 1))) {  
            return filename.substring(dot + 1);  
        }  
    }  
    return filename;  
}

public static String getFileType(String type) {  
    String documents = "txt doc pdf ppt pps xlsx xls docx";  
    String music = "mp3 wav wma mpa ram ra aac aif m4a";  
    String video = "avi mpg mpe mpeg asf wmv mov qt rm mp4 flv m4v webm ogv ogg";  
    String image = "bmp dib pcp dif wmf gif jpg tif eps psd cdr iff tga pcd mpt png jpeg";  
    if (image.contains(type)) {  
        return "pic";  
    } else if (documents.contains(type)) {  
        return "txt";  
    } else if (music.contains(type)) {  
        return "music";  
    } else if (video.contains(type)) {  
        return "vedio";  
    } else {  
        return "other";  
    }  
}


```

```java
package com.ruoyi.common.core.utils.ip;  
  
import java.net.InetAddress;  
import java.net.UnknownHostException;  
import javax.servlet.http.HttpServletRequest;  
import com.ruoyi.common.core.utils.StringUtils;  
  
/**  
 * 获取IP方法  
 *   
* @author zpc  
 */public class IpUtils  
{  
    /**  
     * 获取客户端IP  
     ** @param request 请求对象  
     * @return IP地址  
     */  
    public static String getIpAddr(HttpServletRequest request)  
    {  
        if (request == null)  
        {  
            return "unknown";  
        }  
        String ip = request.getHeader("x-forwarded-for");  
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip))  
        {  
            ip = request.getHeader("Proxy-Client-IP");  
        }  
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip))  
        {  
            ip = request.getHeader("X-Forwarded-For");  
        }  
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip))  
        {  
            ip = request.getHeader("WL-Proxy-Client-IP");  
        }  
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip))  
        {  
            ip = request.getHeader("X-Real-IP");  
        }  
  
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip))  
        {  
            ip = request.getRemoteAddr();  
        }  
  
        return "0:0:0:0:0:0:0:1".equals(ip) ? "127.0.0.1" : getMultistageReverseProxyIp(ip);  
    }  
  
    /**  
     * 检查是否为内部IP地址  
     *   
* @param ip IP地址  
     * @return 结果  
     */  
    public static boolean internalIp(String ip)  
    {  
        byte[] addr = textToNumericFormatV4(ip);  
        return internalIp(addr) || "127.0.0.1".equals(ip);  
    }  
  
    /**  
     * 检查是否为内部IP地址  
     *   
* @param addr byte地址  
     * @return 结果  
     */  
    private static boolean internalIp(byte[] addr)  
    {  
        if (StringUtils.isNull(addr) || addr.length < 2)  
        {  
            return true;  
        }  
        final byte b0 = addr[0];  
        final byte b1 = addr[1];  
        // 10.x.x.x/8  
        final byte SECTION_1 = 0x0A;  
        // 172.16.x.x/12  
        final byte SECTION_2 = (byte) 0xAC;  
        final byte SECTION_3 = (byte) 0x10;  
        final byte SECTION_4 = (byte) 0x1F;  
        // 192.168.x.x/16  
        final byte SECTION_5 = (byte) 0xC0;  
        final byte SECTION_6 = (byte) 0xA8;  
        switch (b0)  
        {  
            case SECTION_1:  
                return true;  
            case SECTION_2:  
                if (b1 >= SECTION_3 && b1 <= SECTION_4)  
                {  
                    return true;  
                }  
            case SECTION_5:  
                switch (b1)  
                {  
                    case SECTION_6:  
                        return true;  
                }  
            default:  
                return false;  
        }  
    }  
  
    /**  
     * 将IPv4地址转换成字节  
     *   
* @param text IPv4地址  
     * @return byte 字节  
     */  
    public static byte[] textToNumericFormatV4(String text)  
    {  
        if (text.length() == 0)  
        {  
            return null;  
        }  
  
        byte[] bytes = new byte[4];  
        String[] elements = text.split("\\.", -1);  
        try        {  
            long l;  
            int i;  
            switch (elements.length)  
            {  
                case 1:  
                    l = Long.parseLong(elements[0]);  
                    if ((l < 0L) || (l > 4294967295L))  
                    {  
                        return null;  
                    }  
                    bytes[0] = (byte) (int) (l >> 24 & 0xFF);  
                    bytes[1] = (byte) (int) ((l & 0xFFFFFF) >> 16 & 0xFF);  
                    bytes[2] = (byte) (int) ((l & 0xFFFF) >> 8 & 0xFF);  
                    bytes[3] = (byte) (int) (l & 0xFF);  
                    break;                case 2:  
                    l = Integer.parseInt(elements[0]);  
                    if ((l < 0L) || (l > 255L))  
                    {  
                        return null;  
                    }  
                    bytes[0] = (byte) (int) (l & 0xFF);  
                    l = Integer.parseInt(elements[1]);  
                    if ((l < 0L) || (l > 16777215L))  
                    {  
                        return null;  
                    }  
                    bytes[1] = (byte) (int) (l >> 16 & 0xFF);  
                    bytes[2] = (byte) (int) ((l & 0xFFFF) >> 8 & 0xFF);  
                    bytes[3] = (byte) (int) (l & 0xFF);  
                    break;                case 3:  
                    for (i = 0; i < 2; ++i)  
                    {  
                        l = Integer.parseInt(elements[i]);  
                        if ((l < 0L) || (l > 255L))  
                        {  
                            return null;  
                        }  
                        bytes[i] = (byte) (int) (l & 0xFF);  
                    }  
                    l = Integer.parseInt(elements[2]);  
                    if ((l < 0L) || (l > 65535L))  
                    {  
                        return null;  
                    }  
                    bytes[2] = (byte) (int) (l >> 8 & 0xFF);  
                    bytes[3] = (byte) (int) (l & 0xFF);  
                    break;                case 4:  
                    for (i = 0; i < 4; ++i)  
                    {  
                        l = Integer.parseInt(elements[i]);  
                        if ((l < 0L) || (l > 255L))  
                        {  
                            return null;  
                        }  
                        bytes[i] = (byte) (int) (l & 0xFF);  
                    }  
                    break;  
                default:  
                    return null;  
            }  
        }  
        catch (NumberFormatException e)  
        {  
            return null;  
        }  
        return bytes;  
    }  
  
    /**  
     * 获取IP地址  
     *   
* @return 本地IP地址  
     */  
    public static String getHostIp()  
    {  
        try  
        {  
            return InetAddress.getLocalHost().getHostAddress();  
        }  
        catch (UnknownHostException e)  
        {  
        }  
        return "127.0.0.1";  
    }  
  
    /**  
     * 获取主机名  
     *   
* @return 本地主机名  
     */  
    public static String getHostName()  
    {  
        try  
        {  
            return InetAddress.getLocalHost().getHostName();  
        }  
        catch (UnknownHostException e)  
        {  
        }  
        return "未知";  
    }  
  
    /**  
     * 从多级反向代理中获得第一个非unknown IP地址  
     *  
     * @param ip 获得的IP地址  
     * @return 第一个非unknown IP地址  
     */  
    public static String getMultistageReverseProxyIp(String ip)  
    {  
        // 多级反向代理检测  
        if (ip != null && ip.indexOf(",") > 0)  
        {  
            final String[] ips = ip.trim().split(",");  
            for (String subIp : ips)  
            {  
                if (false == isUnknown(subIp))  
                {  
                    ip = subIp;  
                    break;                }  
            }  
        }  
        return ip;  
    }  
  
    /**  
     * 检测给定字符串是否为未知，多用于检测HTTP请求相关  
     *  
     * @param checkString 被检测的字符串  
     * @return 是否未知  
     */  
    public static boolean isUnknown(String checkString)  
    {  
        return StringUtils.isBlank(checkString) || "unknown".equalsIgnoreCase(checkString);  
    }  
}
``` 

# 文件流上传
```Java
 long begin=System.currentTimeMillis();  
 InputStream fis=multipartFile.getInputStream();  
 dest = new File(newFileName);  
 FileOutputStream fos=new FileOutputStream(dest);  
 //先定义一个字节缓冲区，减少I/O次数，提高读写效率  
 byte[] buffer=new byte[10240];  
 int size=0;  
 while((size=fis.read(buffer))!=-1){  
     fos.write(buffer, 0, size);  
 }  
 fis.close();  
 fos.close();  
 long end=System.currentTimeMillis();  
log.info("使用文件输入流和文件输出流实现文件的复制完毕！耗时：{} 毫秒",(end-begin));
```

# COS文件上传

