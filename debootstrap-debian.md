# **Memasang Debian dengan metode Debootstrap**.

## **Langkah-langkah**

1. Persiapan partisi.
2. Mount filesystem.
3. Memasang base system.
4. Masuk chroot.
5. Edit fstab.
6. Konfigurasi source apt.
7. Memilih zona waktu.
8. Konfigurasi locales.
9. Memasang kernel.
10. Memasang firmware.
11. Set hostname.
12. Konfigurasi jaringan.
13. Memasang bootloader.
14. Mengatur password root.
15. Membuat user dan passwordnya.
16. Memasang bootloader pada disk.
17. Keluar chroot.
18. Unmount beberapa filesystem.
19. Reboot.
20. Paket-paket rekomendasi (minimal wayland setup).

## **Detail**

1. **Persiapan partisi**, partisi minimal yang perlu dipersiapkan di antaranya :

- 	Partisi root (/).

- 	Partisi efi (untuk mesin dengan UEFI).
 
	Format partisi :

> $ sudo mkfs.ext4 /dev/abc1

> $ sudo mkfs.vfat -F 32 /dev/abc2 <br>
<br>
 
2. **Mount filesystem.**

> $ sudo mount /dev/abc1 /mnt

> $ sudo mkdir -p /mnt/boot/efi

> $ sudo mount /dev/abc2 /mnt/boot/efi

<br>

3. **Memasang base system.**

	Debian (debootstrap debian stable) :
	
> $ sudo debootstrap --arch amd64 stable /mnt https://deb.debian.org/debian

<br>

4. **Masuk chroot.**

> $ cd /mnt  

> $ sudo mount -t proc proc proc/

> $ sudo mount --rbind /sys sys/

> $ sudo mount --rbind /dev dev/

> $ sudo mount --rbind /run run/

> $ sudo chroot /mnt /bin/bash

<br>

5. **Edit fstab.**

	Kita bisa menyalin bagian dari berkas **/proc/mounts**

> $ cp /proc/mounts /etc/fstab

	Note :
	- edit seperlunya dan bisa mengacu pada layout fstab dari host.

	- UUID bisa didapatkan dari blkid.

Contoh :


> UUID=2d3b91ab-4936-4fb1-add6-b820adbd0b6c /               ext4    errors=remount-ro 0 1

> UUID=9e484c2f-c74a-4865-a49b-a5b71a5ee220 none            swap    sw              0       0

<br>

6. **Konfigurasi source apt.**

> vim /etc/apt/sources.list

Contoh isi sources.list :

- Debian stable
> deb http://deb.debian.org/debian/ stable main contrib non-free

- Debian testing
> deb http://deb.debian.org/debian/ testing main contrib non-free

- Debian unstable
> deb http://deb.debian.org/debian/ sid main contrib non-free

<br>

7. **Memilih zona waktu.**

> dpkg-reconfigure tzdata

<br>

8. **Konfigurasi locales.**

> apt install locales

> dpkg-reconfigure locales 

<br>

9. **Memasang kernel.**

> apt install linux-image-amd64

<br>

10. **Memasang firmware (bisa opsional).**

> apt install firmware-linux

<br>

11. **Set hostname.**

> echo "Nama_hostname" > /etc/hostname

<br>

12. **Konfigurasi jaringan.**

> vim /etc/network/interfaces 

Contoh isi file interfaces sederhana (koneksi utama dari ethernet) :

> auto eth0 \
iface eth0 inet dhcp

<br>

13. **Memasang paket bootloader.**
	
- Legacy Bios

>apt install grub

- UEFI

>apt install grub-efi-amd64 

<br>

14. **Mengatur password root.**

> passwd 

<br>

15. **Membuat user dan passwordnya.**

> useradd USERNAME -m \
> passwd USERNAME

<br>

16. **Memasang bootloader pada disk.**

- Legacy :

> grub-install /dev/sda 

> update-grub

- UEFI :

> grub-install --target=x86_64-efi --efi-directory=/boot/efi \
update-grub

<br>

17. **Keluar chroot.**

> exit

<br>

18. **Unmount beberapa filesystem.**

> $ sudo umount /mnt/dev 

> $ sudo umount /mnt/sys 

> $ sudo umount /mnt/proc

> $ sudo umount /mnt/run 

> $ sudo umount /mnt 

<br>

19. **Reboot.**

> $ sudo systemctl reboot

<br>

20. **Paket-paket rekomendasi.**

* Vim.
* Qutebrowser.
* Minimal wayland setup :
	- Sway / Weston.
	- foot / qterminal.

