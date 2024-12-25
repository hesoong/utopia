---
title: 笔记｜IP 获取
date: 2018-10-31
updated: 2018-10-31
toc: true
tags:
  - 笔记
---
## 获取本机ip

通过`NetworkInterface`获取

```java
String ip = request.getRemoteAddr();
System.out.println("--->"+ip);
Enumeration<NetworkInterface> n = NetworkInterface.getNetworkInterfaces();

for (; n.hasMoreElements();)
{
	NetworkInterface e = n.nextElement();
	System.out.println("Interface: " + e.getName());
	Enumeration<InetAddress> a = e.getInetAddresses();
	for (; a.hasMoreElements();)
	{
		InetAddress addr = a.nextElement();
		if (!addr.isLinkLocalAddress() 
				&& !addr.isLoopbackAddress()
				&& addr instanceof Inet4Address) {
					System.out.println("  " + addr.getHostAddress());
			}
	}
}
```

## 根据网卡获取ip

根据物理网卡获取本机IP

```java
/**
 * 根据网卡获得IP地址
 * @return
 * @throws SocketException
 * @throws UnknownHostException
 */
public static String getIpAdd() throws SocketException, UnknownHostException {
	String LOCAL_HOST_IP = "127.0.0.1";
	String ip = LOCAL_HOST_IP;
	for (Enumeration<NetworkInterface> en = NetworkInterface.getNetworkInterfaces(); en.hasMoreElements();) {
		NetworkInterface intf = en.nextElement();
		String name = intf.getName();
		if (!name.contains("docker") && !name.contains("lo")) {
			for (Enumeration<InetAddress> enumIpAddr = intf.getInetAddresses(); enumIpAddr.hasMoreElements();) {
				// 获得IP
				InetAddress inetAddress = enumIpAddr.nextElement();
				if (!inetAddress.isLoopbackAddress()) {
					String ipaddress = inetAddress.getHostAddress().toString();
					if (!ipaddress.contains("::") && !ipaddress.contains("0:0:") && !ipaddress.contains("fe80")) {
						logger.debug("IP:" + ipaddress);
						if (!LOCAL_HOST_IP.equals(ipaddress)) {
							return ipaddress;
						}
					}
				}
			}
		}
	}
	return ip;
}
```

### IP转换为int

```java
public static int toIp(String ipAddress) {
        String[] address = StringUtils.split(ipAddress, ".");
        if (address.length != 4) {
            StringBuilder builder = new StringBuilder();
            builder.append("Invalid IP format \"").append(ipAddress).append("\", should be XXX.XXX.XXX.XXX");
            throw new IllegalArgumentException(builder.toString());
        } else {
            int result = 0;

            for (int i = 0; i < address.length; ++i) {
                int segment = Integer.parseInt(address[i]);
                if (segment < 0 || segment >= 256) {
                    StringBuilder builder = new StringBuilder();
                    builder.append("Invalid IP format \"").append(ipAddress).append("\"");
                    throw new IllegalArgumentException(builder.toString());
                }

                result = result << 8 | segment;
            }

            return result;
        }
    }
```

