# Pre-Install
* Ubuntu 18.04 (JetPack 4.5.1 기준)
* micro USB 케이블
* 별도의 sd카드 리더기

# Pre-Download
[Download - BSP](https://www.avermedia.com/professional/download/en715#ans_part|parentHorizontalTab3) 에서 최신 **NX**이미지를 내려받아 우분투에 옮기거나, 우분투 wget 등을 통해 곧바로 내려받는다.<br><br>
버전 예시 : ```Version | EN715-**NX**-R1.0.7.4.5.1```<br>
`wget` 예시 : ```wget https://www.avermedia.com/epaper/file_https.php?file=http://ftp2.avermedia.com/EN715/EN715-NX-R1.0.7.4.5.1.zip```
<br><br>
내려받은 이미지를 우분투 내에서 압축 해제한다. 이때 반드시 sudo를 통해 압축 해제 하여야 한다. (일부 파일이 root 권한)<br><br>
```sudo cat 파일명.tar.gz* | tar xvfz - ```<br><br>

아래 경로로 이동한다. 이 경로를 여러 관련 문서에서 L4T로 보통 많이 줄여쓰니 기억해둔다.<br>
```cd JetPack_4.5.1_Linux_JETSON_XAVIER_NX/Linux_for_Tegra```

# SD카드
## sd카드 마운팅 포인트 확인
```
sudo fdisk -l | grep sd
microsd 파티션 확인.
(ex /dev/sdb일때, 아래 명령어 중 sdx대신 sdb 입력)
```

## sd카드 포맷
여기서 \<Enter\> 부분은 실제 입력 아니고, 엔터로 개행한다.<br>
```
sudo gdisk /dev/sdx
o<Enter>
y<Enter>
n<Enter>
1<Enter>
40M<Enter>
<Enter>
<Enter>
c<Enter>
PARTLABEL<Enter>
w<Enter>
y<Enter>
```

## Jetson 이미지 복사
```
sudo mkfs.ext4 /dev/sdx1
sudo blkid /dev/sdx1
```

위에서 `blkid`로 얻은 `PARTUUID`를 기억한다.

```
sudo mount /dev/sdx1 /mnt
nano bootloader/l4t-rootfs-uuid.txt_ext
위에서 blkid로 확인한 partuuid 입력 후 저장
cd rootfs
sudo tar -cpf - * | ( cd /mnt/ ; sudo tar -xpf - )
sudo umount /mnt
```

## 리커버리 모드 진입
`power off -> press recovery button -> power on -> wait 2 seconds -> release recovery button` <br><br>
  
# Flash
```
cd ~/path/to/Linux_for_Tegra
sudo ./flash.sh jetson-xavier-nx-en715 external
```

이후 부팅 시 externel로 안될 경우 아래 명령어로 다시 플래시한다.

```sudo ./flash.sh jetson-xavier-nx-en715 mmcblk1p1```

# Jetson 부팅
젯슨 **전원 차단 후**, microSD 카드를 삽입하고, 젯슨을 다시 부팅한다.
