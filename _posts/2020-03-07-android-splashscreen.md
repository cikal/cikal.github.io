---
layout: post
title: "Cara Buat Splashscreen Aplikasi Android"
date: 2020-03-07 04:25:00 +0700
categories: android
tags: [android,java]
thumbnail: "/assets/img/thumbnail/lMnm2jeTix.png"
timeread: 10 min
excerpt_separator: <!--more-->
comments: true
comment_button: false
related: true
---
Karena kesibukan job freelance beberapa minggu terakhir, baru hari ini saya bisa menulis lagi. Baiklah tanpa berpanjang lebar<!--more-->, kali ini saya akan share Cara Buat Splashscreen Aplikasi Android.

> Apasih kegunaan si Splashscreen ini ?

Splashscreen biasa digunakan untuk branding logo dari pembuat aplikasi, contohnya Gojek, Grab, YouTube, dan masih banyak lagi aplikasi lainnya.

> Kapan sebaiknya Splashscreen digunakan ?

Splashscreen baiknya dan umum digunakan pada saat aplikasi pertama kali dijalankan, dengan kata lain. Si Splashscreen ini hanya muncul satu kali di awal.


Baiklah tanpa berpanjang lebar lagi, mari kita langsung ke poin tutorial Cara Buat Splashscreen Aplikasi Android.

---

### Persiapan

* Buka __Android Studio__
* Buat sebuah project baru
* Secangkir Kopi dan cemilan.

---

#### Buat Project Baru pada Android Studio

1. Silahkan buka Android Studio anda
2. Buat sebuah project :
   * Nama project pada tutorial ini : __Demo Splashscreen__
   * Min SDK pada tutorial ini : __API 21 Android 5.0 (Lolipop)__ (sesuaikan saja sama Min SDK milik anda)
   * Activity yang digunakan : __Empty Activity__
   * Activity Name : __SplashActivity__
   * Layout Name : __activity_splash__
3. Tunggu beberapa saat hingga project anda selesai di build.

Setelah project berhasil di build, maka secara default file `SplashActivity.java` dan `activity_splash.xml` otomatis terbuka pada editor Android Studio.

Eeiitss, tahan dulu ngodingnya. Jangan sampai lagi enak-enak ngoding disuruh bikin "__New Empty Activity__", kan bengong nungguin build sama indexing nya.. _LoL.. :p_ Yuuk aah, lanjut.

Silahkan buat __Empty Activity__ baru, kemudian beri nama `MainActivity` dan layout name nya `activity_main`.

![Buat Activity Baru](/assets/img/studio_lpjgA8E4N4.png){:.img-fluid}

Jika proses build dan indexing nya dah kelar, mari seruput kopi dan mulai ngoding.

#### File `MainActivity.java`

Pada file ini tidak ada yang berubah sedikitpun, karena file ini berfungsi sebagai Landing setelah Splashscreen selesai ditampilkan.

```java
package io.github.cikal.demosplashscreen;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
}
```

---

#### File `SplashActivity.java`

File ini adalah file utama yang kita bahas pada tutorial Cara Buat Splashscreen Aplikasi Android, silahkan dipelajari kodingan nya.

```java
package io.github.cikal.demosplashscreen;

import android.content.Intent;
import android.os.Handler;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;

public class SplashActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_splash);

        // deklarasikan integer untuk durasi
        // satuan yang digunakan miliseconds
        // 1000 ms = 1 sec
        final int durasi_splash = 3000; // 3 detik

        new Handler().postDelayed(new Runnable() {
            @Override
            public void run() {
                // panggil activity MainActivity
                startActivity(new Intent(SplashActivity.this, MainActivity.class));
                // untuk menghancurkan SplashActivity dari history
                finish();
            }
        }, durasi_splash); // masukkan durasi splash disini
    }
}
```

Sedikit penjelasan,

* Jika anda datang dari bahasa program __JavaScript__ maka ini familiar untuk anda, satuan hitung mundur yang digunakan MiliSeconds (MS). Dimana 1000 MS = Segelas Susu MilKita.. wkwkwk.. `1000 ms = 1 sec`
  * method `new Handler().postDelayed(new Runnable(){...}, MS)`
  * sama dengan method `setTimeout(function(){...}, MS)` pada __JavaScript__
* Pada `Runnable(){ run(){ ... } }` saya memanggil `finish()` yaa seperti yang kita semua ketahui, klo dah finish kan kelar urusan.. yang artinya Activity tersebut kita hapus dari history `onBackPressed()`

---

#### File `activity_main.xml`

Udah urusan sama `Java`, kita main ke `XML`. disini kita poles dikit file Landing nya, oh iya pasti ntar pada error dibagian `ImageView`, silahkan klik kanan di `res > drawable > New > Vector Asset` nanti keluar popup pilihan icon, buanyak pilihan nya monggo dipilih. Di tutorial ini, saya memilih icon centang `ic_check` silahkan anda sesuaikan dengan icon pilihan anda.

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#ddd"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:background="@android:color/white"
        android:gravity="center"
        android:orientation="horizontal"
        android:padding="16sp">

        <!-- ini icon nya dibuat dulu -->
        <ImageView
            android:layout_width="34sp"
            android:layout_height="34sp"
            android:layout_marginEnd="8sp"
            android:src="@drawable/ic_check" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Berhasil"
            android:textColor="#000"
            android:textSize="26sp" />
    </LinearLayout>
</RelativeLayout>
```

---

#### File `activity_splash.xml`

Nah ini nih, file yang dari tadi kita bahas. Disini kita bisa branding Logo Perusahaan / Aplikasi yang kita buat, karena ini tutorial sederhana. Jadi saya buatkan yang se-sederhana mungkin, agar anda bisa berkreasi lebih jauh dengan Fantasi dan ide-ide ~~Liar, Nakal~~ kreatif yang kalian miliki.. wkwkwk..

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/colorPrimary"
    tools:context=".SplashActivity">

    <!--
      2 TextView ini bisa kalian hapus / ganti dengan
      1 ImageView yang berisi Logo / Icon App kalian
    -->

    <TextView
        android:id="@+id/tv_atas"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:text="Demo Splashscreen"
        android:textColor="@android:color/white"
        android:textSize="30sp"
        android:textStyle="bold" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_marginBottom="30sp"
        android:gravity="center"
        android:text="versi 1.0\nhttps://cikal.github.io"
        android:textColor="@android:color/white" />

</RelativeLayout>
```

---

#### File `styles.xml`

File ini akan otomatis ada pada saat anda membuat project, fungsinya untuk mempercantik tampilan layaknya `CSS` pada Web Programing. Disini saya menambahkan style baru, agar layar menjadi FullScreen tanpa mengganggu Style pada Layout lainnya.

```xml
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>

    <!-- ini style tambahan -->
    <style name="AppTheme.NoActionBar">
        <!-- Hilangkan title -->
        <item name="windowNoTitle">true</item>
        <!-- Hilangkan ActionBar -->
        <item name="windowActionBar">false</item>
        <!-- Buat tampilan FullScreen -->
        <item name="android:windowFullscreen">true</item>
    </style>

</resources>
```

---

#### File `AndroidManifest.xml`

Tidak terasa kita sudah tiba di penghujung tutorial Cara Buat Splashscreen Aplikasi Android, langkah terakhir. Mari kita terapkan __styles__ yang baru kita buat pada langkah sebelumnya untuk `SplashActivity`.

(Lihat di _Line 14_)

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="io.github.cikal.demosplashscreen">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity
            android:name=".SplashActivity"
            android:theme="@style/AppTheme.NoActionBar">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity android:name=".MainActivity" />
    </application>

</manifest>
```

Jika semua sudah selesai, silahkan __Running__ project anda pada Emulator (jika PC anda gacor) atau colok HP ke PC / Laptop via USB (seperti yang saya lakukan).. :)

![Demo Splashscreen](/assets/img/demo_splashscreen.gif)

---

Bagaimana? Cukup mudah dipahami bukan tutorial nya. Jika ada yang kurang dimengerti silahkan coret dikolom komentar, sampai jumpa di tutorial yang akan datang.

---

### Summary

> Code is art's