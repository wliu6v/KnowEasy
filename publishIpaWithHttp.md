通过 Http 来发布 ipa
==================================


服务器 IPA/PLIST/HTML 文件配置：

1. 部署 ipa 文件。拷贝 ipa 安装包到 HTTP 目标应用服务器根目录。

2. 创建 plist 文件。注意 software-package、bundle-identifier、subtitle、title 等字符串配置。

		<?xml version="1.0" encoding="UTF-8"?>
		<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
		<plist version="1.0">
		<dict>
			<key>items</key>
			<array>
				<dict>
					<key>assets</key>
					<array>
						<dict>
							<key>kind</key>
							<string>software-package</string> 
							<key>url</key>
							<string>http://192.168.0.28:8000/firmware_updater.ipa</string>
						</dict>
						<dict>
							<key>kind</key>
							<string>display-image</string>
							<key>needs-shine</key>
							<true/>
							<key>url</key>
							<string></string>
						</dict>
						<dict>
							<key>kind</key>
							<string>full-size-image</string>
							<key>needs-shine</key>
							<true/>
							<key>url</key>
							<string></string>
						</dict>
					</array>
					<key>metadata</key>
					<dict>
						<key>bundle-identifier</key>
						<string>net.peerpath.firmware_updater</string>
						<key>bundle-version</key>
						<string>1.0.0</string>
						<key>kind</key>
						<string>software</string>
						<key>subtitle</key>
						<string>firmware_updater</string>
						<key>title</key>
						<string>firmware_updater</string>
					</dict>
				</dict>
			</array>
		</dict>
		</plist>

3. 修改 HTML 文件，创建一个“itms-services”协议的链接指定本地一个 plist 文件。

		<html>
			<head></head>
			<body>
				<a href="itms-services://?action=download-manifest;url=http://192.168.0.28:8000/firewareupdater.plist">Install</a></span></p>
			</body>
		</html>

--------------------------------------------------------------------------------
