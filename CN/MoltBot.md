已配置完成。Moltbot 现在会：                                                  

  - 开机自动启动                                                                

  - 崩溃后自动重启（KeepAlive）                                                 

  管理命令：                                                                    

  # 停止                                                                        

  launchctl unload ~/Library/LaunchAgents/com.moltbot.gateway.plist             

  # 启动                                                                        

  launchctl load ~/Library/LaunchAgents/com.moltbot.gateway.plist               

  # 查看状态                                                                    

  launchctl list | grep moltbot