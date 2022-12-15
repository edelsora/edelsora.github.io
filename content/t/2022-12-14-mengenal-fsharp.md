---
title: Cerita Saya Mengenal F#
description: Bahasa pemrograman fungsional yang praktis, berfungsi dan magic.
---

Awal perkenalan saya dengan F# (dibaca, F sharp) dimulai dari mencari bahasa alternatif dari ReasonML/OCaml yang
saya gunakan untuk belajar pemrograman (paradigma) fungsional karena ekosistem bahasa tersebut kurang
mendukung untuk bidang saya : Web dan Game, sampai saya bertemu dengan [artikel ini](https://blog.darklang.com/leaving-ocaml/).

F# adalah bahasa pemrograman dari ekosistem .NET (dibaca, dot net) dengan paduan paradigma fungsional, imperatif dan _object-oriented_ serta ditambah pernak-pernik keluarga *Meta-Language* seperti _Algebraic Data Type_ dan _Pattern Matching_. 

- OCaml

```ocaml
type habitat_type = Terestrial | Akuatik

type biologis_type =  Hewan of habitat_type | Tumbuhan of habitat_type

type biodata = { name: string; types: biologis_type}

let ceritakan_biodata (b: biodata) =
    match b.types with
    | Hewan habitat ->
        match habitat with
        | Terestrial -> print_endline (Printf.sprintf "%s hidup di darat" b.name)
        | Akuatik -> print_endline (Printf.sprintf "%s hidup di air" b.name)
    | Tumbuhan habitat ->
        match habitat with
        | Terestrial -> print_endline (Printf.sprintf "%s tumbuh di darat" b.name)
        | Akuatik -> print_endline (Printf.sprintf "%s tumbuh di air" b.name)

let main () =
    let me = { name="Akasia"; types= Hewan Terestrial} in
    ceritakan_biodata(me)
```

- F#

```f#
type HabitatType = Terestrial | Akuatik

type BiologisType =  Hewan of HabitatType | Tumbuhan of HabitatType

type Biodata = { name: string; types: BiologisType}

let ceritakanBiodata (b: Biodata) =
    match b.types with
    | Hewan habitat ->
        match habitat with
        | Terestrial -> print_endline (Printf.sprintf "%s hidup di darat" b.name)
        | Akuatik -> print_endline (Printf.sprintf "%s hidup di air" b.name)
    | Tumbuhan habitat ->
        match habitat with
        | Terestrial -> print_endline (Printf.sprintf "%s tumbuh di darat" b.name)
        | Akuatik -> print_endline (Printf.sprintf "%s tumbuh di air" b.name)
    
let main _ =
    let me = { name="Akasia"; types = Hewan Terestrial} 
    ceritakanBiodata(me)
```

F# bisa menggunakan pustaka dari ekosistem .NET yang sudah _mature_ seperti ASP.NET dan Entity Framework
dengan pendekatan fungsional.

## Sentuhan Pertama

Saat menginstall F# pertama kali, saya sudah dibuat bingung dengan pilihanya karena jika kamu ingin menginstall kompiler
F# maka kamu harus menginstall .NET _Software Development Kit_ (SDK) yang sudah termasuk C# dan F# didalamnya.

Jadi kamu tidak bisa menginstall hanya salah satu saja dan uniknya kode yang ditulis di C# ataupun F# bisa memanggil fungsi satu sama lain.

_Culture shock_ lainya yaitu perihal membuat proyek, .NET memiliki 2 jenis struktur _codebase_ seperti _project_ dan _solution_. 
_project_ merupakan wadah tempat kamu ngoding seperti `go mod init` di Go atau `pnpm init` pada Javascript. 

Kemudian yang terakhir _solution_  yang berarti kumpulan dari banyak proyek. Celakanya saat awal mencoba F# saya membuat _solution_ yang saya kira tadinya seperti membuat _project_, yang akhirnya membuat saya makin kebingungan.

Terakhir yaitu _project_ template, ketika kamu membuat sebuah _project_ baru kamu akan disuguhi berbagai macam template untuk memulai
_codebase_. Hal ini memang sangat membantu melakukan scaffolding _project_ dengan cepat tanpa repot install pustaka manual.

Saya berasal dari bahasa yang memulai dari _scratch_ (nulis dari 0) seperti Go atau Python (saya tahu banyak template tapi tidak _built-in_ dan kurang _enforced_ kedalam _workflow_ koding) ini juga membingungkan dan terlihat seperti *wut gini doang?* karena kebiasaan _strat from scratch_. 

Pilihan yang paling sesuai untuk _scratcher_ seperti saya yaitu template _console_ sebuah template _bare minimum_ sebuah program baris perintah untuk kamu memulai _project_,  `dotnet new console -n <nama project> -lang F#`. 

Berikut daftar template yang menurut saya penting:

| nama | deskripsi | cara install template |
|--|--|--|
| `console` | Console app | built-in |
| `webapi` | ASP.NET Core Web API |  built-in |
| `classlib` | Class Library | built-in |
| `giraffe` | Giraffe template |  `dotnet new -i "giraffe-template::*"` |
| `SAFE` | SAFE-stack template | `dotnet new -i SAFE.Template` |
| `sln` | Solution | built-in |
| `xunit` | xUnit unit test | built-in |


Kesimpulan yang saya dapat dari bersentuhan ini adalah ekosistem .NET menekankan _developer experience_. Saya terilhami oleh cara [Vercel](https://vercel.com) membantu _deployment_ yang biasanya ruwet menjadi _zero config_ (tetap kamu masih bisa _override config_ nya) begitu pula yang ingin dicapai .NET dengan ekosistemnya.

Hambatan yang saya temui adalah saya perlu membiasakan diri dengan ".NET way" agar melakukan sesuatu menjadi lebih mudah dan tanpa membuang mentalitas _scratcher_ yang tentunya berguna untuk eksplorasi teknologi lain.

## Kenikmatan

Sentuhan pertama saya dengan .NET terutama F# terkesan seperti mobil yang kepater dijalan berlumpur saat pulang kampung, perlu beberapa penyesuaian pada mindset agar dapat berkerja melalui kacamata ekosistem .NET dan merasakan kenikmatanya.

Hal yang pertama saya coba lepaskan yakni perdebat para _scratcher_ seperti _ORM Bad and Raw Driver Good_, _Zero-allocation shithole_, _Cutting Edge Techstack_. Disini saya coba berpikir naif dan fokus kepada implementasi dengan pustaka yang sudah mature (`boring techstack`), tentunya ASP.NET dkk.

Biasanya saya skeptis dengan pemilihan pustaka, sekarang saya belajar tidak peduli. Ketidakpedulian ini bukan tanpa dasar, hal ini terjadi ketika saya melihat performa F# di laptop saya. 

Kinerja yang dicapai cukup mengherankan, kompilasi cepat, kinerja cepat meskipun pustaka (Giraffe dan Entity Framework) yang saya pakai terlihat besar dan membantah stigma paradigma fungsional memiliki kinerja yang miring (tetapi sekarang, tidak selambat kabarnya). 

Kemangkusan tata bahasa dari F# yang hampir identik dengan OCaml memberikan saya produktivitas yang berbeda, saya lebih memiliki _clarity_ dengan kode-kode yang saya baca dan tulis bahkan baru mencoba beberapa hari saya sudah melakukan [kontribusi _open-source_](https://github.com/Dolfik1/Funogram/pull/53).

Hal yang membuat saya lebih memiliki _clarity_ adalah kejelasan dari _best pratice_ dan _design pattern_ yang memberi saya perasaan *Oh memang begini caranya*. Salah satu contohnya bernama [_Railway oriented programming_](https://fsharpforfunandprofit.com/rop/):

```f#
let ceritakanBiodata (b: Biodata) =
    match b.types with
    | Hewan habitat ->
        match habitat with
        | Terestrial -> print_endline (Printf.sprintf "%s hidup di darat" b.name)
        | Akuatik -> print_endline (Printf.sprintf "%s hidup di air" b.name)
    | Tumbuhan habitat ->
        match habitat with
        | Terestrial -> print_endline (Printf.sprintf "%s tumbuh di darat" b.name)
        | Akuatik -> print_endline (Printf.sprintf "%s tumbuh di air" b.name)
```

Kode diatas ibarat sebuah persimpangan kereta API, setiap hasil dihadapi dengan dua pilihan. Saya
pikir ini cara yang buruk karena harus menangani seluruh nilai yang ada, ternyata memang begini caranya.

Saat saya menggunakan OCaml menjumpai hal seperti tetapi banyak orang yang menulis *"smart code"* (seperti : `>>=`, `>|=`) untuk menghindari penulisan diatas yang juga bingung mereka dapat dari mana (minim sumber yang saya dapat terkait pemahaman operator demikian).

Tetapi di F# hal seperti di _encourage_ mesikpun terlihat _dumb code_ sehingga saya percaya diri dan mendapatkan _sense of magnitude_
: "Oh begini ngoding F#".

### Kasus pakai

F# merupakan bahasa tingkat tinggi untuk kelas aplikasi (antonimnya, kelas sistem). Implementasi digunakan untuk membuat:

- [_web fullstack/service_](https://fsharp.org/guides/web), 
- [_enterprice application service_](https://fsharp.org/testimonials/), 
- [_mobile apps_](https://fsharp.org/guides/mobile-apps/)
- [_data science_](https://fslab.org/tutorials.html).

Saya menggunakan F# untuk kasus _web service_, _web fullstack_ dan bot telegram. Bagi saya untuk mengatakan F# sebagai bahasa FTW (`for the win`) baru berlaku untuk Web. 

Di sisi lain saya merasa _Game Developement_ lebih baik diserahkan ke paradigma _object-oriented_, maka untuk game saya masih memihak `Haxe` tanpa mengurangi antusias saya mengenal F# lebih lanjut.

## Kesimpulan

F# dapat dikatakan OCaml-nya ekosistem .NET dan lebih memiliki konten yang mendorong penggunaan penyelesaian masalah secara
 praktikal ketimbang perseturan akademia (seperti `functor` dan semacanya di komunitas OCaml). OCaml tidak buruk bahkan menurut saya
 bagus disuatu sisi, hanya saja komunitasnya yang cukup bernuansa akademik memerlukan _briddiging_ untuk memahaminya dan ekosistemnya yang sedang mencoba untuk _mature_ dengan kebutuhan industri.

Merasakan kenikmatan _developer experience_  .NET perlu penyesuaian mental model juga untuk memetakan bagaimana kita berkerja dan 
mengetahui _best pratices_ melakukan sesuatu disana, seperti saya kebiasaan memulai membuat web dengan sign in dan sign up dengan 
memulai dengan table `users` dan ternyata cara tersebut sudah di scaffold menggunakan ASP.NET Identity sehingga tidak perlu membuat table manual.

Sejauh saya menulis ini, masih ada banyak hal yang tidak ketahui dari ekosistem .NET yang _magic_ dalam mempersingkat _workflow_ pekerjaan, maka akan saya tulis dilain waktu ketika saya sudah cukup mengenalnya.

Wejangan penutup, pandangan pertama saat saya mendengar kata .NET, saya teingat stigma nya yang buruk yakni: _closed-source_ dan mahal, 
bahkan kala itu pengembangan hanya bisa menggunakan Visual Studio yang juga berbayar. Ternyata stigma tersebut sudah tidak belaku lagi, sekarang ekosistem .NET sebagian besar sudah di rilis secara _open-source_ termasuk C# dan F#. 

Kode editornya pun sudah tidak ekslusif di Visual Studio, sekarang kamu bisa mengembangkan program berbasis .NET di Visual Studio Code (meskipun saya tahu mereka sesama besutan Microsoft), dan tentunya ini cukup mengejutkan karena saya ingat Visual Basic 6 itu berbayar dan itu bahasa pemrograman pertama saya.

EOF