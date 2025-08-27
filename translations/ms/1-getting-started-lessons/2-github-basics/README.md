<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "05666cecb8983a72cf0ce1d18932b5b7",
  "translation_date": "2025-08-27T23:21:43+00:00",
  "source_file": "1-getting-started-lessons/2-github-basics/README.md",
  "language_code": "ms"
}
-->
# Pengenalan kepada GitHub

Pelajaran ini merangkumi asas GitHub, sebuah platform untuk menghos dan mengurus perubahan pada kod anda.

![Intro to GitHub](../../../../translated_images/webdev101-github.8846d7971abef6f947909b4f9d343e2a23778aa716ca6b9d71df7174ee5009ac.ms.png)
> Sketchnote oleh [Tomomi Imura](https://twitter.com/girlie_mac)

## Kuiz Pra-Kuliah
[Kuiz pra-kuliah](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/3)

## Pengenalan

Dalam pelajaran ini, kita akan membincangkan:

- menjejaki kerja yang anda lakukan pada mesin anda
- bekerja pada projek bersama orang lain
- cara menyumbang kepada perisian sumber terbuka

### Prasyarat

Sebelum anda bermula, anda perlu memeriksa sama ada Git telah dipasang. Dalam terminal taip: 
`git --version`

Jika Git belum dipasang, [muat turun Git](https://git-scm.com/downloads). Kemudian, tetapkan profil Git tempatan anda dalam terminal:
* `git config --global user.name "nama-anda"`
* `git config --global user.email "emel-anda"`

Untuk memeriksa sama ada Git telah dikonfigurasi, anda boleh taip:
`git config --list`

Anda juga memerlukan akaun GitHub, editor kod (seperti Visual Studio Code), dan anda perlu membuka terminal anda (atau: command prompt).

Navigasi ke [github.com](https://github.com/) dan buat akaun jika anda belum melakukannya, atau log masuk dan lengkapkan profil anda. 

✅ GitHub bukan satu-satunya repositori kod di dunia; terdapat yang lain, tetapi GitHub adalah yang paling terkenal.

### Persediaan

Anda memerlukan folder dengan projek kod pada mesin tempatan anda (laptop atau PC), dan repositori awam di GitHub, yang akan berfungsi sebagai contoh bagaimana untuk menyumbang kepada projek orang lain.  

---

## Pengurusan Kod

Katakan anda mempunyai folder secara tempatan dengan projek kod dan anda ingin mula menjejaki kemajuan anda menggunakan git - sistem kawalan versi. Sesetengah orang membandingkan penggunaan git dengan menulis surat cinta kepada diri anda di masa depan. Membaca mesej komit anda beberapa hari, minggu, atau bulan kemudian, anda akan dapat mengingat mengapa anda membuat keputusan, atau "rollback" perubahan - iaitu, apabila anda menulis mesej komit yang baik.

### Tugasan: Buat repositori dan komit kod  

> Tonton video
> 
> [![Video asas Git dan GitHub](https://img.youtube.com/vi/9R31OUPpxU4/0.jpg)](https://www.youtube.com/watch?v=9R31OUPpxU4)

1. **Buat repositori di GitHub**. Di GitHub.com, dalam tab repositori, atau dari bar navigasi di bahagian atas kanan, cari butang **new repo**.

   1. Berikan nama kepada repositori anda (folder)
   1. Pilih **create repository**.

1. **Navigasi ke folder kerja anda**. Dalam terminal anda, tukar ke folder (juga dikenali sebagai direktori) yang anda ingin mula jejaki. Taip:

   ```bash
   cd [name of your folder]
   ```

1. **Inisialisasi repositori git**. Dalam projek anda taip:

   ```bash
   git init
   ```

1. **Periksa status**. Untuk memeriksa status repositori anda taip:

   ```bash
   git status
   ```

   outputnya boleh kelihatan seperti ini:

   ```output
   Changes not staged for commit:
   (use "git add <file>..." to update what will be committed)
   (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   file.txt
        modified:   file2.txt
   ```

   Biasanya arahan `git status` memberitahu anda perkara seperti fail mana yang sedia untuk _disimpan_ ke repositori atau mempunyai perubahan yang mungkin anda ingin kekalkan.

1. **Tambah semua fail untuk penjejakan**
   Ini juga dipanggil sebagai fail pementasan/menambah fail ke kawasan pementasan.

   ```bash
   git add .
   ```

   Argumen `git add` ditambah `.` menunjukkan bahawa semua fail & perubahan anda untuk penjejakan. 

1. **Tambah fail terpilih untuk penjejakan**

   ```bash
   git add [file or folder name]
   ```

   Ini membantu kita menambah hanya fail terpilih ke kawasan pementasan apabila kita tidak mahu komit semua fail sekaligus.

1. **Batalkan pementasan semua fail**

   ```bash
   git reset
   ```

   Arahan ini membantu kita membatalkan pementasan semua fail sekaligus.

1. **Batalkan pementasan fail tertentu**

   ```bash
   git reset [file or folder name]
   ```

   Arahan ini membantu kita membatalkan pementasan hanya fail tertentu sekaligus yang kita tidak mahu sertakan untuk komit seterusnya.

1. **Kekalkan kerja anda**. Pada ketika ini anda telah menambah fail ke kawasan yang dipanggil _staging area_. Tempat di mana Git menjejaki fail anda. Untuk menjadikan perubahan kekal, anda perlu _commit_ fail tersebut. Untuk melakukannya, anda membuat _commit_ dengan arahan `git commit`. Komit mewakili titik simpanan dalam sejarah repositori anda. Taip arahan berikut untuk membuat _commit_:

   ```bash
   git commit -m "first commit"
   ```

   Ini mengkomit semua fail anda, dengan mesej "first commit". Untuk mesej komit masa depan, anda akan mahu lebih deskriptif dalam penerangan anda untuk menyampaikan jenis perubahan yang telah anda buat.

1. **Sambungkan repositori Git tempatan anda dengan GitHub**. Repositori Git adalah baik pada mesin anda tetapi pada satu ketika anda ingin mempunyai sandaran fail anda di suatu tempat dan juga menjemput orang lain untuk bekerja dengan anda pada repositori anda. Salah satu tempat yang hebat untuk melakukannya ialah GitHub. Ingat kita telah membuat repositori di GitHub jadi satu-satunya perkara yang perlu kita lakukan ialah menyambungkan repositori Git tempatan kita dengan GitHub. Arahan `git remote add` akan melakukannya. Taip arahan berikut:

   > Nota, sebelum anda menaip arahan pergi ke halaman repositori GitHub anda untuk mencari URL repositori. Anda akan menggunakannya dalam arahan di bawah. Gantikan ```https://github.com/username/repository_name.git``` dengan URL GitHub anda.

   ```bash
   git remote add origin https://github.com/username/repository_name.git
   ```

   Ini mencipta _remote_, atau sambungan, bernama "origin" yang menunjuk kepada repositori GitHub yang anda buat sebelum ini.

1. **Hantar fail tempatan ke GitHub**. Setakat ini anda telah mencipta _connection_ antara repositori tempatan dan repositori GitHub. Mari hantar fail ini ke GitHub dengan arahan berikut `git push`, seperti ini: 
   
   > Nota, nama cawangan anda mungkin berbeza secara lalai daripada ```main```.

   ```bash
   git push -u origin main
   ```

   Ini menghantar komit anda dalam cawangan "main" anda ke GitHub.

2. **Untuk menambah lebih banyak perubahan**. Jika anda ingin terus membuat perubahan dan menghantarnya ke GitHub, anda hanya perlu menggunakan tiga arahan berikut:

   ```bash
   git add .
   git commit -m "type your commit message here"
   git push
   ```

   > Tip, Anda mungkin juga ingin menggunakan fail `.gitignore` untuk mengelakkan fail yang anda tidak mahu jejaki daripada muncul di GitHub - seperti fail nota yang anda simpan dalam folder yang sama tetapi tidak sesuai untuk repositori awam. Anda boleh mencari templat untuk fail `.gitignore` di [.gitignore templates](https://github.com/github/gitignore).

#### Mesej Komit

Baris subjek komit Git yang hebat melengkapkan ayat berikut:
Jika digunakan, komit ini akan <baris subjek anda di sini>

Untuk subjek gunakan bentuk imperatif, masa kini: "ubah" bukan "diubah" atau "mengubah". 
Seperti dalam subjek, dalam badan (pilihan) juga gunakan bentuk imperatif, masa kini. Badan harus merangkumi motivasi untuk perubahan dan bandingkan ini dengan tingkah laku sebelumnya. Anda menerangkan `mengapa`, bukan `bagaimana`.

✅ Luangkan beberapa minit untuk melayari GitHub. Bolehkah anda menemui mesej komit yang sangat hebat? Bolehkah anda menemui yang sangat minimal? Maklumat apa yang anda fikir paling penting dan berguna untuk disampaikan dalam mesej komit?

### Tugasan: Bekerjasama

Sebab utama meletakkan perkara di GitHub adalah untuk memungkinkan kerjasama dengan pembangun lain.

## Bekerja pada projek bersama orang lain

> Tonton video
>
> [![Video asas Git dan GitHub](https://img.youtube.com/vi/bFCM-PC3cu8/0.jpg)](https://www.youtube.com/watch?v=bFCM-PC3cu8)

Dalam repositori anda, navigasi ke `Insights > Community` untuk melihat bagaimana projek anda dibandingkan dengan piawaian komuniti yang disyorkan.

   Berikut adalah beberapa perkara yang boleh meningkatkan repositori GitHub anda:
   - **Deskripsi**. Adakah anda menambah deskripsi untuk projek anda?
   - **README**. Adakah anda menambah README? GitHub menyediakan panduan untuk menulis [README](https://docs.github.com/articles/about-readmes/?WT.mc_id=academic-77807-sagibbon).
   - **Panduan penyumbangan**. Adakah projek anda mempunyai [panduan penyumbangan](https://docs.github.com/articles/setting-guidelines-for-repository-contributors/?WT.mc_id=academic-77807-sagibbon), 
   - **Kod Etika**. [Kod Etika](https://docs.github.com/articles/adding-a-code-of-conduct-to-your-project/), 
   - **Lesen**. Mungkin yang paling penting, [lesen](https://docs.github.com/articles/adding-a-license-to-a-repository/)?


Semua sumber ini akan memberi manfaat kepada onboarding ahli pasukan baru. Dan ini biasanya perkara yang dilihat oleh penyumbang baru sebelum melihat kod anda, untuk mengetahui sama ada projek anda adalah tempat yang sesuai untuk mereka meluangkan masa mereka.

✅ Fail README, walaupun memerlukan masa untuk disediakan, sering diabaikan oleh penyelenggara yang sibuk. Bolehkah anda menemui contoh yang sangat deskriptif? Nota: terdapat beberapa [alat untuk membantu mencipta README yang baik](https://www.makeareadme.com/) yang mungkin anda ingin cuba.

### Tugasan: Gabungkan kod

Dokumen penyumbangan membantu orang menyumbang kepada projek. Ia menerangkan jenis sumbangan yang anda cari dan bagaimana prosesnya berfungsi. Penyumbang perlu melalui beberapa langkah untuk dapat menyumbang kepada repositori anda di GitHub:

1. **Fork repositori anda** Anda mungkin mahu orang _fork_ projek anda. Fork bermaksud mencipta replika repositori anda pada profil GitHub mereka.
1. **Clone**. Dari situ mereka akan clone projek ke mesin tempatan mereka. 
1. **Buat cawangan**. Anda akan mahu meminta mereka membuat _branch_ untuk kerja mereka. 
1. **Fokuskan perubahan mereka pada satu kawasan**. Minta penyumbang untuk menumpukan sumbangan mereka pada satu perkara pada satu masa - dengan cara itu peluang untuk anda _merge_ kerja mereka adalah lebih tinggi. Bayangkan mereka menulis pembaikan bug, menambah ciri baru, dan mengemas kini beberapa ujian - bagaimana jika anda mahu, atau hanya boleh melaksanakan 2 daripada 3, atau 1 daripada 3 perubahan?

✅ Bayangkan situasi di mana cawangan sangat kritikal untuk menulis dan menghantar kod yang baik. Apakah kes penggunaan yang boleh anda fikirkan?

> Nota, jadilah perubahan yang anda ingin lihat di dunia, dan buat cawangan untuk kerja anda sendiri juga. Sebarang komit yang anda buat akan dibuat pada cawangan yang anda sedang "checked out". Gunakan `git status` untuk melihat cawangan mana itu.

Mari kita lalui aliran kerja penyumbang. Anggap penyumbang telah _forked_ dan _cloned_ repositori jadi mereka mempunyai repositori Git yang sedia untuk digunakan, pada mesin tempatan mereka:

1. **Buat cawangan**. Gunakan arahan `git branch` untuk membuat cawangan yang akan mengandungi perubahan yang mereka maksudkan untuk disumbangkan:

   ```bash
   git branch [branch-name]
   ```

1. **Tukar ke cawangan kerja**. Tukar ke cawangan yang ditentukan dan kemas kini direktori kerja dengan `git switch`:

   ```bash
   git switch [branch-name]
   ```

1. **Lakukan kerja**. Pada ketika ini anda ingin menambah perubahan anda. Jangan lupa untuk memberitahu Git mengenainya dengan arahan berikut:

   ```bash
   git add .
   git commit -m "my changes"
   ```

   Pastikan anda memberikan komit anda nama yang baik, untuk kebaikan anda serta penyelenggara repositori yang anda bantu.

1. **Gabungkan kerja anda dengan cawangan `main`**. Pada satu ketika anda selesai bekerja dan anda ingin menggabungkan kerja anda dengan cawangan `main`. Cawangan `main` mungkin telah berubah sementara itu jadi pastikan anda mengemas kini terlebih dahulu kepada yang terkini dengan arahan berikut:

   ```bash
   git switch main
   git pull
   ```

   Pada ketika ini anda ingin memastikan bahawa sebarang _conflicts_, situasi di mana Git tidak dapat dengan mudah _combine_ perubahan berlaku dalam cawangan kerja anda. Oleh itu jalankan arahan berikut:

   ```bash
   git switch [branch_name]
   git merge main
   ```

   Ini akan membawa masuk semua perubahan dari `main` ke dalam cawangan anda dan semoga anda boleh teruskan. Jika tidak, VS Code akan memberitahu anda di mana Git _keliru_ dan anda hanya mengubah fail yang terjejas untuk mengatakan kandungan mana yang paling tepat.

1. **Hantar kerja anda ke GitHub**. Menghantar kerja anda ke GitHub bermaksud dua perkara. Menolak cawangan anda ke repositori anda dan kemudian membuka PR, Pull Request.

   ```bash
   git push --set-upstream origin [branch-name]
   ```

   Arahan di atas mencipta cawangan pada repositori yang telah anda fork.

1. **Buka PR**. Seterusnya, anda ingin membuka PR. Anda melakukannya dengan menavigasi ke repositori yang telah anda fork di GitHub. Anda akan melihat petunjuk di GitHub di mana ia bertanya sama ada anda ingin mencipta PR baru, anda klik itu dan anda dibawa ke antara muka di mana anda boleh menukar tajuk mesej komit, memberikan penerangan yang lebih sesuai. Sekarang penyelenggara repositori yang anda fork akan melihat PR ini dan _fingers crossed_ mereka akan menghargai dan _merge_ PR anda. Anda kini seorang penyumbang, yay :)

1. **Bersihkan**. Ia dianggap amalan yang baik untuk _bersihkan_ selepas anda berjaya menggabungkan PR. Anda ingin membersihkan kedua-dua cawangan tempatan anda dan cawangan yang anda tolak ke GitHub. Pertama mari kita hapuskannya secara tempatan dengan arahan berikut: 

   ```bash
   git branch -d [branch-name]
   ```
Pastikan anda pergi ke halaman GitHub untuk repo yang telah di-fork dan hapus cawangan jauh yang baru sahaja anda tolak ke sana.

`Pull request` nampaknya seperti istilah yang kurang sesuai kerana sebenarnya anda ingin menolak perubahan anda ke projek tersebut. Tetapi penyelenggara (pemilik projek) atau pasukan teras perlu mempertimbangkan perubahan anda sebelum menggabungkannya dengan cawangan "utama" projek, jadi anda sebenarnya meminta keputusan perubahan daripada penyelenggara.

Pull request adalah tempat untuk membandingkan dan membincangkan perbezaan yang diperkenalkan pada cawangan dengan ulasan, komen, ujian yang terintegrasi, dan banyak lagi. Pull request yang baik mengikuti peraturan yang hampir sama seperti mesej commit. Anda boleh menambah rujukan kepada isu dalam penjejak isu, contohnya apabila kerja anda menyelesaikan sesuatu isu. Ini dilakukan dengan menggunakan `#` diikuti dengan nombor isu anda. Sebagai contoh, `#97`.

🤞Semoga semua pemeriksaan lulus dan pemilik projek menggabungkan perubahan anda ke dalam projek🤞

Kemas kini cawangan kerja tempatan anda dengan semua commit baru dari cawangan jauh yang sepadan di GitHub:

`git pull`

## Cara menyumbang kepada sumber terbuka

Pertama, mari kita cari repositori (atau **repo**) di GitHub yang menarik minat anda dan yang ingin anda sumbangkan perubahan. Anda perlu menyalin kandungannya ke mesin anda.

✅ Cara yang baik untuk mencari repo yang mesra pemula adalah dengan [mencari menggunakan tag 'good-first-issue'](https://github.blog/2020-01-22-browse-good-first-issues-to-start-contributing-to-open-source/).

![Salin repo secara tempatan](../../../../translated_images/clone_repo.5085c48d666ead57664f050d806e325d7f883be6838c821e08bc823ab7c66665.ms.png)

Terdapat beberapa cara untuk menyalin kod. Salah satu cara adalah "clone" kandungan repositori, menggunakan HTTPS, SSH, atau menggunakan GitHub CLI (Command Line Interface).

Buka terminal anda dan clone repositori seperti ini:
`git clone https://github.com/ProjectURL`

Untuk bekerja pada projek, tukar ke folder yang betul:
`cd ProjectURL`

Anda juga boleh membuka keseluruhan projek menggunakan [Codespaces](https://github.com/features/codespaces), editor kod terbenam GitHub / persekitaran pembangunan awan, atau [GitHub Desktop](https://desktop.github.com/).

Akhir sekali, anda boleh memuat turun kod dalam folder yang telah dimampatkan (zipped).

### Beberapa perkara menarik tentang GitHub

Anda boleh memberi bintang, menonton, dan/atau "fork" mana-mana repositori awam di GitHub. Anda boleh mencari repositori yang telah anda bintangi dalam menu drop-down di bahagian atas kanan. Ia seperti penanda buku, tetapi untuk kod.

Projek mempunyai penjejak isu, kebanyakannya di GitHub dalam tab "Issues" kecuali dinyatakan sebaliknya, di mana orang membincangkan isu berkaitan projek. Dan tab Pull Requests adalah tempat orang membincangkan dan mengulas perubahan yang sedang berlangsung.

Projek mungkin juga mempunyai perbincangan dalam forum, senarai mel, atau saluran sembang seperti Slack, Discord atau IRC.

✅ Lihat sekeliling repo GitHub baru anda dan cuba beberapa perkara, seperti mengedit tetapan, menambah maklumat kepada repo anda, dan mencipta projek (seperti papan Kanban). Terdapat banyak yang boleh anda lakukan!

---

## 🚀 Cabaran

Berpasangan dengan rakan untuk bekerja pada kod masing-masing. Cipta projek secara kolaboratif, fork kod, cipta cawangan, dan gabungkan perubahan.

## Kuiz Selepas Kuliah
[Kuiz selepas kuliah](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/4)

## Ulasan & Kajian Kendiri

Baca lebih lanjut tentang [menyumbang kepada perisian sumber terbuka](https://opensource.guide/how-to-contribute/#how-to-submit-a-contribution).

[Cheatsheet Git](https://training.github.com/downloads/github-git-cheat-sheet/).

Berlatih, berlatih, berlatih. GitHub mempunyai laluan pembelajaran yang hebat tersedia melalui [skills.github.com](https://skills.github.com):

- [Minggu Pertama di GitHub](https://skills.github.com/#first-week-on-github)

Anda juga akan menemui kursus yang lebih maju.

## Tugasan

Lengkapkan [kursus Minggu Pertama di GitHub](https://skills.github.com/#first-week-on-github)

---

**Penafian**:  
Dokumen ini telah diterjemahkan menggunakan perkhidmatan terjemahan AI [Co-op Translator](https://github.com/Azure/co-op-translator). Walaupun kami berusaha untuk memastikan ketepatan, sila ambil perhatian bahawa terjemahan automatik mungkin mengandungi kesilapan atau ketidaktepatan. Dokumen asal dalam bahasa asalnya harus dianggap sebagai sumber yang berwibawa. Untuk maklumat yang kritikal, terjemahan manusia profesional adalah disyorkan. Kami tidak bertanggungjawab atas sebarang salah faham atau salah tafsir yang timbul daripada penggunaan terjemahan ini.