---
title: GPS坐标转换为百度地图坐标
date: 2016-09-11 15:52:25
categories: Web
tags:
 - 离线地图
 - GPS
 - 百度地图
---

记录一个定位系统相关的项目，这个项目应用场景是要在离线地图下使用GPS采集的坐标进行地图定位。在项目中遇到了一个问题，就是GPS采集的坐标需要进行转换才能在百度地图上定位，而百度地图的加密算法没有进行公开，但在搜索了大量的资料后终于找到了一个可行的转换算法。
现分享一下这个GPS坐标转换为百度地图坐标解决方案
* * * * *
### GPS的坐标系
> WGS-84是GPS坐标系遵循的标准，从串口读取到GPS的数据包就是这个标准下的格式。GPS的数据帧根据帧头可分类为GPGGA、GPGSA、GPGSV、GPRMC等，我们需要的经纬度、时间可以在GPRMC帧中获取

<!--more-->

**串口接收到GPS数据包**
``` stylus
$GPGGA,103401.468,,,,,0,00,50.0,,M,0.0,M,,0000*40
$GPGSA,A,1,,,,,,,,,,,,,,,*1E
$GPRMC,103401.468,V,,,,,,,270716,,,N*45
$GPGGA,103402.468,,,,,0,00,50.0,,M,0.0,M,,0000*43
$GPGSA,A,1,,,,,,,,,,,,,,,*1E
$GPRMC,103402.468,V,,,,,,,270716,,,N*46
$GPGGA,103403.468,,,,,0,00,50.0,,M,0.0,M,,0000*42
$GPGSA,A,1,,,,,,,,,,,,,,,*1E
$GPGSV,3,1,12,05,58,277,25,06,37,061,17,02,51,358,,12,30,262,*78
$GPGSV,3,2,12,13,25,179,,04,21,146,,17,21,140,,25,14,297,*7B
$GPGSV,3,3,12,20,14,246,,09,11,042,,29,05,322,,15,03,204,*7E
$GPRMC,103403.468,V,,,,,,,270716,,,N*47
$GPGGA,103404.468,,,,,0,00,50.0,,M,0.0,M,,0000*45
$GPGSA,A,1,,,,,,,,,,,,,,,*1E
$GPRMC,103404.468,V,,,,,,,270716,,,N*40
$GPGGA,103405.468,,,,,0,00,50.0,,M,0.0,M,,0000*44
```
现我们解析下GPS数据包下的数据帧
#### GPGGA
> Global Positioning System Fix Data（GGA）GPS定位信息

$GPGGA,105420.000,2302.6623,N,11312.4958,E,1,07,1.2,32.9,M,-6.8,M,,0000*73
$GPGGA,<1>,<2>,<3>,<4>,<5>,<6>,<7>,<8>,<9>,M,<10>,M,<11>,<12>*hh<CR><LF>

<1> UTC时间，hhmmss（时分秒）格式
> 注解：105420.000表示UTC时间为10时54分20秒，如果换算成北京时间加8个小时就行，即位18时54分20秒。
GPS系统的时间与UTC时间是不同的，差了一个闰秒，因为UTC时间是可以调整的，而GPS时间是连续的，闰秒数在下行的导航电文中有反应。北京时=GPS时+8小时-闰秒。GPGGA和GPRMC中本身已经将GPS时间转换为UTC时间了，所以该时间与北京时只差8小时，GPS时间显示为2月28日0时（假设该年非闰年的话），北京时是2月28日8时；GPS时间为2月27日15时，则北京时间为2月27日23时，；GPS时间为2月27日18时，则北京时间为2月28日2时；GPS时间为2月28日18时，则北京时间为3月1日，2时。

<2> 纬度ddmm.mmmm（度分）格式（前面的0也将被传输）
<3> 纬度半球N（北半球）或S（南半球） 
<4> 经度dddmm.mmmm（度分）格式（前面的0也将被传输） 
<5> 经度半球E（东经）或W（西经） 
<6> GPS状态：0=未定位，1=非差分定位，2=差分定位，6=正在估算 
<7> 正在使用解算位置的卫星数量（00~12）（前面的0也将被传输） 
<8> HDOP水平精度因子（0.5~99.9） 
<9> 海拔高度（-9999.9~99999.9） 
<10> 地球椭球面相对大地水准面的高度 
<11> 差分时间（从最近一次接收到差分信号开始的秒数，如果不是差分定位将为空） 
<12> 差分站ID号0000~1023（前面的0也将被传输，如果不是差分定位将为空）
* * * * *
#### GPRMC
> Recommended Minimum Specific GPS/TRANSIT Data（RMC）推荐定位信息
$GPRMC,105422.000,A,2302.6623,N,11312.4957,E,0.17,51.46,190716,,,A*5B
$GPRMC,<1>,<2>,<3>,<4>,<5>,<6>,<7>,<8>,<9>,<10>,<11>,<12>*hh<CR><LF>
<1> UTC时间，hhmmss（时分秒）格式 
<2> 定位状态，A=有效定位，V=无效定位 
<3> 纬度ddmm.mmmm（度分）格式（前面的0也将被传输） 
<4> 纬度半球N（北半球）或S（南半球） 
<5> 经度dddmm.mmmm（度分）格式（前面的0也将被传输） 
<6> 经度半球E（东经）或W（西经） 
<7> 地面速率（000.0~999.9节，前面的0也将被传输） 
<8> 地面航向（000.0~359.9度，以真北为参考基准，前面的0也将被传输） 
<9> UTC日期，ddmmyy（日月年）格式 
<10> 磁偏角（000.0~180.0度，前面的0也将被传输） 
<11> 磁偏角方向，E（东）或W（西） 
<12> 模式指示（仅NMEA0183 3.00版本输出，A=自主定位，D=差分，E=估算，N=数据无效）
* * * * *
#### GPGGA
>  GPS DOP and Active Satellites（GSA）当前卫星信息

$GPGSA,A,3,05,19,02,06,12,17,13,09,,,,,2.2,0.9,2.0*3E
$GPGSA,<1>,<2>,<3>,<3>,,,,,<3>,<3>,<3>,<4>,<5>,<6>,<7><CR><LF>

<1>模式 ：M = 手动， A = 自动。 
<2>定位型式 1 = 未定位， 2 = 二维定位， 3 = 三维定位。 
<3>PRN 数字：01 至 32 表天空使用中的卫星编号，最多可接收12颗卫星信息。 
<4> PDOP位置精度因子（0.5~99.9） 
<5> HDOP水平精度因子（0.5~99.9） 
<6> VDOP垂直精度因子（0.5~99.9） 
<7> Checksum.(检查位).
* * * * *

#### GPGSV
> GPS Satellites in View（GSV）可见卫星信息

$GPGSV,3,2,12,12,32,270,22,17,26,136,20,13,19,181,14,09,15,046,08*78
$GPGSV, <1>,<2>,<3>,<4>,<5>,<6>,<7>,?<4>,<5>,<6>,<7>,<8><CR><LF>
<1> GSV语句的总数 
<2> 本句GSV的编号 
<3> 可见卫星的总数，00 至 12。 
<4> 卫星编号， 01 至 32。 
<5>卫星仰角， 00 至 90 度。 
<6>卫星方位角， 000 至 359 度。实际值。 
<7>讯号噪声比（C/No）， 00 至 99 dB；无表未接收到讯号。 
<8>Checksum.(检查位).
第<4>,<5>,<6>,<7>项个别卫星会重复出现，每行最多有四颗卫星。其余卫星信息会于次一行出现，若未使用，这些字段会空白。

### GPS坐标转换算法
> GPS采集原始数据格式是纬度ddmm.mmmmmm（度分）格式、经度dddmm.mmmmm（度分）格式，要先转换为定位地图的数据格式dd.dddddd

1.GPS（WGS-84）格式转换：
3040.8639,N,10405.7573,E 根据度分格式解析为北纬30°40.8639'，东经104°5.7573' ，然后根据度分秒转换成格式为dd.dddddd的小数形式

``` stylus
		String lons = "4349.2353";
		String lats = "08737.4384";
		String lon_gps[] = {lons.substring(0,2),lons.substring(2,4),lons.substring(5)};
		String lat_gps[] = {lats.substring(0,3),lats.substring(3,5),lats.substring(6)};
		double lon = Double.parseDouble(lon_gps[0]) + Double.parseDouble(lon_gps[1])/60 +  Double.parseDouble(lon_gps[2])/100/3600;
		double lat =  Double.parseDouble(lat_gps[0]) + Double.parseDouble(lat_gps[1])/60 +  Double.parseDouble(lat_gps[2])/100/3600;
		System.out.println(lon+"=======" + lat);
```
2.GPS（BD-09）格式转换：
> WGS-84的坐标是国际标准，而百度地图定位的加密标准是BD-09(在GCJ-02基础二次加密)。

算法复杂直接用java代码展示吧
``` stylus
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		
		String lons = "4349.2353";//gps纬度
		String lats = "08737.4384";//gps经度
		String lon_gps[] = {lons.substring(0,2),lons.substring(2,4),lons.substring(5)};
		String lat_gps[] = {lats.substring(0,3),lats.substring(3,5),lats.substring(6)};
		double lon = Double.parseDouble(lon_gps[0]) + Double.parseDouble(lon_gps[1])/60 +  Double.parseDouble(lon_gps[2])/100/3600;
		double lat =  Double.parseDouble(lat_gps[0]) + Double.parseDouble(lat_gps[1])/60 +  Double.parseDouble(lat_gps[2])/100/3600;
		System.out.println(lon+"=======" + lat);
		double db[] = wgs2bd(lat, lon);//WGS坐标转换百度坐标
		System.out.println(db[0]+"=="+db[1]);
	}

public static double[] wgs2bd(double lat, double lon) {
	       double[] wgs2gcj = wgs2gcj(lat, lon);
	       double[] gcj2bd = gcj2bd(wgs2gcj[0], wgs2gcj[1]);
	       return gcj2bd;
	}

	public static double[] gcj2bd(double lat, double lon) {
	       double x = lon, y = lat;
	       double z = Math.sqrt(x * x + y * y) + 0.00002 * Math.sin(y * x_pi);
	       double theta = Math.atan2(y, x) + 0.000003 * Math.cos(x * x_pi);
	       double bd_lon = z * Math.cos(theta) + 0.0065;
	       double bd_lat = z * Math.sin(theta) + 0.006;
	       return new double[] { bd_lat, bd_lon };
	}

	public static double[] bd2gcj(double lat, double lon) {
	       double x = lon - 0.0065, y = lat - 0.006;
	       double z = Math.sqrt(x * x + y * y) - 0.00002 * Math.sin(y * x_pi);
	       double theta = Math.atan2(y, x) - 0.000003 * Math.cos(x * x_pi);
	       double gg_lon = z * Math.cos(theta);
	       double gg_lat = z * Math.sin(theta);
	       return new double[] { gg_lat, gg_lon };
	}
```

最后感谢：
http://blog.csdn.net/gulansheng/article/details/44496185
http://bbs.lbsyun.baidu.com/forum.php?mod=viewthread&tid=10923
http://www.cnblogs.com/zhaohuionly/archive/2013/06/18/3142623.html
