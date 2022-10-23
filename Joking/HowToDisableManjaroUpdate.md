# 如何禁止 Manjaro 更新

&emsp;&emsp;说到系统更新，我们最先想到的就是臭名昭著的 Windows Update,总在你不知不觉中占用网速，给你偷偷更新系统，你还不能关闭。殊不知，Manjaro 的系统更新更加恐怖：大约每月来一波大更新，一更新就是上 GB 的下载量。另外由于 Manjaro 是基于 Arch Linux 的，它也继承了容易滚挂的坏毛病。接下来猫猫就来教你如何干掉 Manjaro 的更新模块，完全杜绝滚挂：

&emsp;&emsp;负责 Manjaro 更新的是一个叫 `pacman`的程序，位于`/usr/bin`。我们只需这么做：

```shell
sudo dd if=/dev/null of=/usr/bin/pacman
sudo dd if=/dev/null of=/usr/bin/makepkg
sudo dd if=/dev/null of=/usr/bin/makepkg-template
sudo dd if=/dev/null of=/usr/bin/pacman
sudo dd if=/dev/null of=/usr/bin/pacman-conf
sudo dd if=/dev/null of=/usr/bin/pacman-db-upgrade
sudo dd if=/dev/null of=/usr/bin/pacman-key
sudo dd if=/dev/null of=/usr/bin/repo-add
sudo dd if=/dev/null of=/usr/bin/repo-elephant
sudo dd if=/dev/null of=/usr/bin/repo-remove
sudo dd if=/dev/null of=/usr/bin/testpkg
sudo dd if=/dev/null of=/usr/bin/vercmp 
sudo rm -rf /usr/share/makepkg
sudo rm -rf /usr/share/pacman
```

&emsp;&emsp;就可以完全干掉 pacman 这个毒瘤，杜绝 Manjaro 的系统更新。