# Lab Projects 10 to 15 - Java (10-14) + Flutter (15) Complete Code Guide

> Projects 10-14 in **Java (Android Studio)**. Project 15 in **Flutter (VS Code)**.
> Create a new Android Studio project for each Java exercise (Empty Views Activity, Language: Java).

General Android Studio setup for all Java projects:
```bash
# Android Studio -> New Project -> Empty Views Activity -> Language: Java -> Min SDK 24
# build.gradle (app) must contain:
android { compileSdk 34 }
dependencies {
  implementation 'androidx.appcompat:appcompat:1.6.1'
  implementation 'com.google.android.material:material:1.11.0'
}
```

---

## 10. Login and Homepage Application in Java (SDG 4 - Quality Education)

**Aim:** Create an application with login and homepage.

**Files:**
- `activity_login.xml`
- `LoginActivity.java`
- `activity_home.xml`
- `HomeActivity.java`
- `AndroidManifest.xml`

**res/layout/activity_login.xml:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="24dp">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Quality Education Login"
        android:textSize="22sp"
        android:textStyle="bold" />

    <EditText
        android:id="@+id/etEmail"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Email (admin@college.edu)"
        android:inputType="textEmailAddress"
        android:layout_marginTop="20dp" />

    <EditText
        android:id="@+id/etPass"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Password (123456)"
        android:inputType="textPassword"
        android:layout_marginTop="12dp" />

    <Button
        android:id="@+id/btnLogin"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="LOGIN"
        android:layout_marginTop="20dp" />
</LinearLayout>
```

**LoginActivity.java:**
```java
package com.example.loginapp;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;

public class LoginActivity extends AppCompatActivity {
    EditText etEmail, etPass;
    Button btnLogin;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login);

        etEmail = findViewById(R.id.etEmail);
        etPass = findViewById(R.id.etPass);
        btnLogin = findViewById(R.id.btnLogin);

        btnLogin.setOnClickListener(v -> {
            String email = etEmail.getText().toString().trim();
            String pass = etPass.getText().toString().trim();

            if (!email.contains("@")) {
                etEmail.setError("Enter valid email");
                return;
            }
            if (pass.length() < 6) {
                etPass.setError("Min 6 chars");
                return;
            }
            // Demo auth
            if (email.equals("admin@college.edu") && pass.equals("123456")) {
                Intent i = new Intent(LoginActivity.this, HomeActivity.class);
                i.putExtra("email", email);
                startActivity(i);
                finish();
            } else {
                Toast.makeText(this, "Invalid credentials", Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```

**res/layout/activity_home.xml:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <TextView
        android:id="@+id/tvWelcome"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="20sp"
        android:textStyle="bold"
        android:text="Welcome" />

    <GridView
        android:id="@+id/gridCourses"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:numColumns="2"
        android:verticalSpacing="12dp"
        android:horizontalSpacing="12dp"
        android:layout_marginTop="16dp" />
</LinearLayout>
```

**HomeActivity.java:**
```java
package com.example.loginapp;

import android.content.Intent;
import android.os.Bundle;
import android.widget.ArrayAdapter;
import android.widget.GridView;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;

public class HomeActivity extends AppCompatActivity {
    String[] courses = {"Maths","Science","Java","English","History","CS"};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_home);

        String email = getIntent().getStringExtra("email");
        TextView tv = findViewById(R.id.tvWelcome);
        tv.setText("Welcome: " + email);

        GridView grid = findViewById(R.id.gridCourses);
        grid.setAdapter(new ArrayAdapter<>(this,
            android.R.layout.simple_list_item_1, courses));

        // Logout on long press title (simple) or add menu button
        tv.setOnLongClickListener(v -> {
            startActivity(new Intent(this, LoginActivity.class));
            finish();
            return true;
        });
    }
}
```

**AndroidManifest.xml (relevant):**
```xml
<activity android:name=".LoginActivity" android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
<activity android:name=".HomeActivity" />
```

---

## 11. Geo-Location Based Service in Java (SDG 11 - Sustainable Cities)

**Aim:** Apply Geo-Location based service in an application.

**build.gradle (app):**
```gradle
dependencies {
  implementation 'com.google.android.gms:play-services-location:21.0.1'
}
```

**AndroidManifest.xml add:**
```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```

**res/layout/activity_geo.xml:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="20dp">

    <TextView android:id="@+id/tvLatLng"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Lat: -, Lng: -"
        android:textSize="18sp"
        android:textStyle="bold" />

    <TextView android:id="@+id/tvAddress"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Press button to locate"
        android:layout_marginTop="8dp" />

    <Button android:id="@+id/btnLocate"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Get My Location"
        android:layout_marginTop="20dp" />

    <Button android:id="@+id/btnMap"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Open in Google Maps"
        android:layout_marginTop="10dp" />

    <TextView android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Nearby (SDG 11 Demo): Hospital 1.2km, School 0.8km, Bus Stop 0.3km"
        android:layout_marginTop="20dp" />
</LinearLayout>
```

**GeoActivity.java:**
```java
package com.example.geoapp;

import android.Manifest;
import android.content.Intent;
import android.content.pm.PackageManager;
import android.location.Address;
import android.location.Geocoder;
import android.net.Uri;
import android.os.Bundle;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;
import com.google.android.gms.location.FusedLocationProviderClient;
import com.google.android.gms.location.LocationServices;
import java.util.List;
import java.util.Locale;

public class GeoActivity extends AppCompatActivity {
    TextView tvLatLng, tvAddress;
    Button btnLocate, btnMap;
    FusedLocationProviderClient client;
    double lat = 0, lng = 0;
    boolean hasLoc = false;
    final int REQ = 100;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_geo);

        tvLatLng = findViewById(R.id.tvLatLng);
        tvAddress = findViewById(R.id.tvAddress);
        btnLocate = findViewById(R.id.btnLocate);
        btnMap = findViewById(R.id.btnMap);
        client = LocationServices.getFusedLocationProviderClient(this);

        btnLocate.setOnClickListener(v -> getLocation());
        btnMap.setOnClickListener(v -> {
            if (!hasLoc) return;
            Uri uri = Uri.parse("geo:" + lat + "," + lng + "?q=" + lat + "," + lng);
            startActivity(new Intent(Intent.ACTION_VIEW, uri));
        });
    }

    void getLocation() {
        if (ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION)
                != PackageManager.PERMISSION_GRANTED) {
            ActivityCompat.requestPermissions(this,
                new String[]{Manifest.permission.ACCESS_FINE_LOCATION}, REQ);
            return;
        }
        client.getLastLocation().addOnSuccessListener(loc -> {
            if (loc != null) {
                lat = loc.getLatitude(); lng = loc.getLongitude();
                hasLoc = true;
                tvLatLng.setText("Lat: " + lat + "\nLng: " + lng);
                try {
                    Geocoder g = new Geocoder(this, Locale.getDefault());
                    List<Address> list = g.getFromLocation(lat, lng, 1);
                    if (list != null && !list.isEmpty()) {
                        Address a = list.get(0);
                        tvAddress.setText(a.getAddressLine(0));
                    }
                } catch (Exception e) {
                    tvAddress.setText("Geocoder error: " + e.getMessage());
                }
            } else {
                Toast.makeText(this, "Enable GPS and retry", Toast.LENGTH_SHORT).show();
            }
        });
    }

    @Override
    public void onRequestPermissionsResult(int c, @NonNull String[] p, @NonNull int[] r) {
        super.onRequestPermissionsResult(c, p, r);
        if (c == REQ && r.length > 0 && r[0] == PackageManager.PERMISSION_GRANTED) getLocation();
        else Toast.makeText(this, "Permission denied", Toast.LENGTH_SHORT).show();
    }
}
```

---

## 12(a). College Portal in Java (SDG 4)

**Aim:** Real-time College Portal - notices, courses, profile.

**res/layout/activity_portal.xml:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <FrameLayout android:id="@+id/frame"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1" />

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNav"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:menu="@menu/portal_menu" />
</LinearLayout>
```

**res/menu/portal_menu.xml:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/nav_notices" android:title="Notices" />
    <item android:id="@+id/nav_courses" android:title="Courses" />
    <item android:id="@+id/nav_profile" android:title="Profile" />
</menu>
```

**PortalActivity.java:**
```java
package com.example.collegeportal;

import android.os.Bundle;
import android.widget.ArrayAdapter;
import android.widget.LinearLayout;
import android.widget.ListView;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;
import com.google.android.material.bottomnavigation.BottomNavigationView;

public class PortalActivity extends AppCompatActivity {
    String[] notices = {
        "Semester Exam Timetable Released - Sep 28",
        "Java Workshop SDG 9 Innovation - Oct 02",
        "Scholarship Applications Open - Oct 05"};
    String[] courses = {
        "CS101 - Java Programming - Dr. A. Kumar",
        "CS102 - Data Structures - Prof. S. Rao",
        "CS103 - DBMS with Firebase - Dr. M. Priya"};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_portal);
        setTitle("College Portal - SDG 4");

        BottomNavigationView nav = findViewById(R.id.bottomNav);
        nav.setOnItemSelectedListener(item -> {
            int id = item.getItemId();
            if (id == R.id.nav_notices) showNotices();
            else if (id == R.id.nav_courses) showCourses();
            else showProfile();
            return true;
        });
        showNotices();
    }

    void showNotices() {
        FrameLayoutWrap(R.layout.view_list, notices, "Live Notices");
    }
    void showCourses() {
        FrameLayoutWrap(R.layout.view_list, courses, "My Courses");
    }
    void showProfile() {
        android.widget.FrameLayout f = findViewById(R.id.frame);
        f.removeAllViews();
        TextView tv = new TextView(this);
        tv.setText("Roll No: 2024CSE001\nName: Student\nAttendance: 87%");
        tv.setTextSize(20); tv.setPadding(32,32,32,32);
        f.addView(tv);
    }

    void FrameLayoutWrap(int layout, String[] data, String title) {
        android.widget.FrameLayout f = findViewById(R.id.frame);
        f.removeAllViews();
        LinearLayout ll = new LinearLayout(this);
        ll.setOrientation(LinearLayout.VERTICAL);
        TextView t = new TextView(this);
        t.setText(title); t.setTextSize(20); t.setPadding(24,24,24,24);
        ListView lv = new ListView(this);
        lv.setAdapter(new ArrayAdapter<>(this,
            android.R.layout.simple_list_item_1, data));
        // Pull-to-refresh simulation: tap to toast
        lv.setOnItemClickListener((a,v,p,i) ->
            android.widget.Toast.makeText(this, "Opened: "+data[p],
                android.widget.Toast.LENGTH_SHORT).show());
        ll.addView(t); ll.addView(lv);
        f.addView(ll);
    }
}
```

> For viva: explain real-time = `SwipeRefreshLayout` + Firebase/REST refresh. Add `SwipeRefreshLayout` around ListView for full marks.

---

## 12(b). Online Quiz in Java (SDG 4)

**Aim:** Online Quiz with timer and auto-evaluation.

**res/layout/activity_quiz.xml:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="20dp">

    <TextView android:id="@+id/tvTimer"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Time: 15s"
        android:textSize="16sp"
        android:textStyle="bold" />

    <ProgressBar android:id="@+id/progress"
        style="?android:attr/progressBarStyleHorizontal"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp" />

    <TextView android:id="@+id/tvQ"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="20sp"
        android:textStyle="bold"
        android:layout_marginTop="20dp" />

    <RadioGroup android:id="@+id/rg"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp">
        <RadioButton android:id="@+id/r0" android:layout_width="match_parent" android:layout_height="wrap_content" />
        <RadioButton android:id="@+id/r1" android:layout_width="match_parent" android:layout_height="wrap_content" />
        <RadioButton android:id="@+id/r2" android:layout_width="match_parent" android:layout_height="wrap_content" />
        <RadioButton android:id="@+id/r3" android:layout_width="match_parent" android:layout_height="wrap_content" />
    </RadioGroup>

    <Button android:id="@+id/btnNext"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="NEXT"
        android:layout_marginTop="20dp" />
</LinearLayout>
```

**QuizActivity.java:**
```java
package com.example.onlinequiz;

import android.content.Intent;
import android.os.Bundle;
import android.os.CountDownTimer;
import android.widget.Button;
import android.widget.ProgressBar;
import android.widget.RadioButton;
import android.widget.RadioGroup;
import android.widget.TextView;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;

public class QuizActivity extends AppCompatActivity {
    String[] questions = {
        "Java is developed by?",
        "SDG 4 stands for?",
        "Firebase is a?",
        "JVM stands for?",
        "Which keyword is used for inheritance in Java?"};
    String[][] opts = {
        {"Apple","Sun Microsystems/Oracle","Meta","Microsoft"},
        {"Climate","Quality Education","Hunger","Water"},
        {"BaaS","OS","IDE","Language"},
        {"Java Virtual Machine","Java Visual Model","Joint Virtual Machine","None"},
        {"extends","implements","inherits","super"}};
    int[] ans = {1,1,0,0,0};

    int cur = 0, score = 0;
    TextView tvQ, tvTimer;
    RadioGroup rg;
    RadioButton[] rbs = new RadioButton[4];
    Button btnNext;
    ProgressBar progress;
    CountDownTimer timer;

    @Override
    protected void onCreate(Bundle s) {
        super.onCreate(s);
        setContentView(R.layout.activity_quiz);
        tvQ = findViewById(R.id.tvQ);
        tvTimer = findViewById(R.id.tvTimer);
        rg = findViewById(R.id.rg);
        btnNext = findViewById(R.id.btnNext);
        progress = findViewById(R.id.progress);
        rbs[0]=findViewById(R.id.r0); rbs[1]=findViewById(R.id.r1);
        rbs[2]=findViewById(R.id.r2); rbs[3]=findViewById(R.id.r3);

        btnNext.setOnClickListener(v -> next());
        loadQ();
    }

    void loadQ() {
        tvQ.setText((cur+1)+". "+questions[cur]);
        for (int i=0;i<4;i++) rbs[i].setText(opts[cur][i]);
        rg.clearCheck();
        progress.setProgress((cur+1)*100/questions.length);
        startTimer();
        btnNext.setText(cur==questions.length-1?"FINISH":"NEXT");
    }

    void startTimer() {
        if (timer!=null) timer.cancel();
        timer = new CountDownTimer(15000,1000) {
            public void onTick(long m){ tvTimer.setText("Time: "+m/1000+"s"); }
            public void onFinish(){ nextAuto(); }
        }.start();
    }

    void nextAuto() {
        Toast.makeText(this,"Time up! Auto next",Toast.LENGTH_SHORT).show();
        next();
    }

    void next() {
        int sel = -1;
        for (int i=0;i<4;i++) if (rbs[i].isChecked()) sel=i;
        if (sel==ans[cur]) score++;
        if (timer!=null) timer.cancel();
        if (cur < questions.length-1) { cur++; loadQ(); }
        else {
            Intent i = new Intent(this, ResultActivity.class);
            i.putExtra("score",score); i.putExtra("total",questions.length);
            startActivity(i); finish();
        }
    }
    @Override protected void onDestroy(){ super.onDestroy(); if(timer!=null) timer.cancel(); }
}
```

**res/layout/activity_result.xml:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="match_parent"
    android:gravity="center" android:orientation="vertical">
    <TextView android:id="@+id/tvScore"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:textSize="40sp" android:textStyle="bold" />
    <TextView android:id="@+id/tvMsg"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:textSize="18sp" />
    <Button android:id="@+id/btnRetry"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:text="RESTART QUIZ" android:layout_marginTop="20dp" />
</LinearLayout>
```

**ResultActivity.java:**
```java
package com.example.onlinequiz;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;

public class ResultActivity extends AppCompatActivity {
    @Override protected void onCreate(Bundle s) {
        super.onCreate(s);
        setContentView(R.layout.activity_result);
        int score = getIntent().getIntExtra("score",0);
        int total = getIntent().getIntExtra("total",5);
        ((TextView)findViewById(R.id.tvScore)).setText(score+" / "+total);
        ((TextView)findViewById(R.id.tvMsg)).setText(
            score >= total*0.6 ? "Excellent! Passed" : "Try Again!");
        ((Button)findViewById(R.id.btnRetry)).setOnClickListener(v -> {
            startActivity(new Intent(this, QuizActivity.class)); finish();
        });
    }
}
```

---

## 13. QR Code Application in Java (SDG 9 - Innovation)

**Aim:** Develop an application with QR Code generation and scanning.

**build.gradle (app):**
```gradle
dependencies {
  implementation 'com.google.zxing:core:3.5.1'
  implementation 'com.journeyapps:zxing-android-embedded:4.3.0'
}
```

**res/layout/activity_qr.xml:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="match_parent"
    android:orientation="vertical" android:padding="20dp">

    <EditText android:id="@+id/etText"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:hint="Enter text / URL"
        android:text="https://college.edu/student/2024CSE001" />

    <Button android:id="@+id/btnGenerate"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:text="Generate QR" android:layout_marginTop="10dp" />

    <ImageView android:id="@+id/ivQR"
        android:layout_width="250dp" android:layout_height="250dp"
        android:layout_gravity="center" android:layout_marginTop="16dp" />

    <Button android:id="@+id/btnScan"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:text="Scan QR" android:layout_marginTop="16dp" />

    <TextView android:id="@+id/tvResult"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:text="Scanned: -" android:textSize="16sp"
        android:layout_marginTop="12dp" />
</LinearLayout>
```

**QrActivity.java:**
```java
package com.example.qrapp;

import android.content.Intent;
import android.graphics.Bitmap;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ImageView;
import android.widget.TextView;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.zxing.BarcodeFormat;
import com.google.zxing.WriterException;
import com.google.zxing.common.BitMatrix;
import com.google.zxing.qrcode.QRCodeWriter;
import com.journeyapps.barcodescanner.ScanContract;
import com.journeyapps.barcodescanner.ScanOptions;
import androidx.activity.result.ActivityResultLauncher;

public class QrActivity extends AppCompatActivity {
    EditText etText; ImageView ivQR; TextView tvResult;

    ActivityResultLauncher<ScanOptions> scanner = registerForActivityResult(
        new ScanContract(), result -> {
            if (result.getContents() != null)
                tvResult.setText("Scanned: " + result.getContents());
        });

    @Override protected void onCreate(Bundle s) {
        super.onCreate(s);
        setContentView(R.layout.activity_qr);
        setTitle("QR Innovation - SDG 9");

        etText = findViewById(R.id.etText);
        ivQR = findViewById(R.id.ivQR);
        tvResult = findViewById(R.id.tvResult);
        Button gen = findViewById(R.id.btnGenerate);
        Button scan = findViewById(R.id.btnScan);

        gen.setOnClickListener(v -> generate(etText.getText().toString()));
        scan.setOnClickListener(v -> {
            ScanOptions o = new ScanOptions();
            o.setPrompt("Scan QR Code");
            o.setBeepEnabled(true);
            o.setOrientationLocked(false);
            scanner.launch(o);
        });
    }

    void generate(String text) {
        QRCodeWriter w = new QRCodeWriter();
        try {
            BitMatrix m = w.encode(text, BarcodeFormat.QR_CODE, 512, 512);
            Bitmap bmp = Bitmap.createBitmap(512,512,Bitmap.Config.RGB_565);
            for (int x=0;x<512;x++) for (int y=0;y<512;y++)
                bmp.setPixel(x,y, m.get(x,y) ? 0xFF000000 : 0xFFFFFFFF);
            ivQR.setImageBitmap(bmp);
        } catch (WriterException e) { e.printStackTrace(); }
    }
}
```

---

## 14. CRUD with Firebase Database in Java (SDG 9)

**Aim:** Perform CRUD operations with Firebase Firestore (Java).

**Setup:**
1. Firebase Console -> New Project -> Build -> Firestore Database -> Create (test mode)
2. Android Studio -> Tools -> Firebase -> Firestore -> Connect + Add dependency
3. Download `google-services.json` -> `app/`
4. Project `build.gradle`: `id 'com.google.gms.google-services' version '4.4.0' apply false`
5. App `build.gradle`:
```gradle
plugins { id 'com.android.application'; id 'com.google.gms.google-services' }
dependencies {
  implementation platform('com.google.firebase:firebase-bom:32.7.0')
  implementation 'com.google.firebase:firebase-firestore'
}
```
6. `AndroidManifest.xml`: `<uses-permission android:name="android.permission.INTERNET"/>`

**res/layout/activity_crud.xml:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="match_parent"
    android:orientation="vertical" android:padding="16dp">

    <EditText android:id="@+id/etName"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:hint="Student Name" />
    <EditText android:id="@+id/etDept"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:hint="Department" />

    <Button android:id="@+id/btnAdd"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:text="ADD STUDENT" android:layout_marginTop="10dp" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rv"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginTop="12dp" />
</LinearLayout>
```

**res/layout/item_student.xml:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="wrap_content"
    android:orientation="horizontal" android:padding="12dp">
    <LinearLayout android:layout_width="0dp" android:layout_height="wrap_content"
        android:layout_weight="1" android:orientation="vertical">
        <TextView android:id="@+id/tvName"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:textStyle="bold" android:textSize="16sp" />
        <TextView android:id="@+id/tvDept"
            android:layout_width="wrap_content" android:layout_height="wrap_content" />
    </LinearLayout>
    <Button android:id="@+id/btnEdit"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:text="EDIT" />
    <Button android:id="@+id/btnDel"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:text="DEL" />
</LinearLayout>
```

**Student.java:**
```java
package com.example.firebasecrud;

public class Student {
    public String id, name, dept;
    public Student(){}
    public Student(String id, String name, String dept){
        this.id=id; this.name=name; this.dept=dept;
    }
}
```

**CrudActivity.java (Create + Read + Update + Delete):**
```java
package com.example.firebasecrud;

import android.app.AlertDialog;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;
import com.google.firebase.firestore.DocumentSnapshot;
import com.google.firebase.firestore.FirebaseFirestore;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class CrudActivity extends AppCompatActivity {
    EditText etName, etDept;
    Button btnAdd;
    RecyclerView rv;
    FirebaseFirestore db;
    List<Student> list = new ArrayList<>();
    StudentAdapter adapter;

    @Override protected void onCreate(Bundle s) {
        super.onCreate(s);
        setContentView(R.layout.activity_crud);
        setTitle("Firebase CRUD - SDG 9");

        etName = findViewById(R.id.etName);
        etDept = findViewById(R.id.etDept);
        btnAdd = findViewById(R.id.btnAdd);
        rv = findViewById(R.id.rv);
        db = FirebaseFirestore.getInstance();

        rv.setLayoutManager(new LinearLayoutManager(this));
        adapter = new StudentAdapter(list,
            // EDIT -> UPDATE
            stu -> {
                EditText e1 = new EditText(this); e1.setText(stu.name);
                EditText e2 = new EditText(this); e2.setText(stu.dept);
                android.widget.LinearLayout ll = new android.widget.LinearLayout(this);
                ll.setOrientation(android.widget.LinearLayout.VERTICAL);
                ll.addView(e1); ll.addView(e2);
                new AlertDialog.Builder(this).setTitle("Update Student")
                    .setView(ll)
                    .setPositiveButton("UPDATE", (d,w) -> {
                        Map<String,Object> m = new HashMap<>();
                        m.put("name", e1.getText().toString());
                        m.put("dept", e2.getText().toString());
                        db.collection("students").document(stu.id)
                          .update(m)
                          .addOnSuccessListener(a -> Toast.makeText(this,"Updated",Toast.LENGTH_SHORT).show());
                    }).setNegativeButton("Cancel", null).show();
            },
            // DELETE
            stu -> db.collection("students").document(stu.id).delete()
                .addOnSuccessListener(a -> Toast.makeText(this,"Deleted",Toast.LENGTH_SHORT).show())
        );
        rv.setAdapter(adapter);

        // CREATE
        btnAdd.setOnClickListener(v -> {
            String n = etName.getText().toString().trim();
            String d = etDept.getText().toString().trim();
            if (n.isEmpty()) { etName.setError("Enter name"); return; }
            Map<String,Object> m = new HashMap<>();
            m.put("name", n); m.put("dept", d);
            m.put("createdAt", com.google.firebase.firestore.FieldValue.serverTimestamp());
            db.collection("students").add(m)
              .addOnSuccessListener(r -> {
                  Toast.makeText(this,"Added",Toast.LENGTH_SHORT).show();
                  etName.setText(""); etDept.setText("");
              });
        });

        // READ (real-time)
        db.collection("students").addSnapshotListener((val, err) -> {
            if (err != null || val == null) return;
            list.clear();
            for (DocumentSnapshot doc : val.getDocuments()) {
                list.add(new Student(doc.getId(),
                    doc.getString("name"), doc.getString("dept")));
            }
            adapter.notifyDataSetChanged();
        });
    }
}
```

**StudentAdapter.java:**
```java
package com.example.firebasecrud;

import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.TextView;
import androidx.annotation.NonNull;
import androidx.recyclerview.widget.RecyclerView;
import java.util.List;

public class StudentAdapter extends RecyclerView.Adapter<StudentAdapter.VH> {
    public interface OnEdit { void edit(Student s); }
    public interface OnDel { void del(Student s); }
    List<Student> list; OnEdit oe; OnDel od;
    public StudentAdapter(List<Student> l, OnEdit e, OnDel d){ list=l; oe=e; od=d; }

    @NonNull @Override public VH onCreateViewHolder(@NonNull ViewGroup p, int v){
        return new VH(LayoutInflater.from(p.getContext())
            .inflate(R.layout.item_student, p, false));
    }
    @Override public void onBindViewHolder(@NonNull VH h, int p){
        Student s = list.get(p);
        h.name.setText(s.name); h.dept.setText(s.dept);
        h.edit.setOnClickListener(v -> oe.edit(s));
        h.del.setOnClickListener(v -> od.del(s));
    }
    @Override public int getItemCount(){ return list.size(); }
    static class VH extends RecyclerView.ViewHolder{
        TextView name, dept; Button edit, del;
        VH(View v){ super(v);
            name=v.findViewById(R.id.tvName);
            dept=v.findViewById(R.id.tvDept);
            edit=v.findViewById(R.id.btnEdit);
            del=v.findViewById(R.id.btnDel);
        }
    }
}
```

**Firestore Rules (test):**
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} { allow read, write: if true; }
  }
}
```

---

## 15. Interactive Flutter App with VS Code (SDG 9) - ONLY FLUTTER

**Aim:** Build an interactive Flutter app using VS Code.

**VS Code Setup Steps:**
1. Install Flutter SDK + VS Code + extensions: `Flutter`, `Dart`
2. `Ctrl+Shift+P` -> `Flutter: New Project` -> select folder
3. Open terminal: `flutter doctor`, `flutter pub get`
4. Press `F5` or `Run > Start Debugging` to run (Chrome / Emulator)
5. Use Hot Reload (`r` in terminal) for interactivity

**pubspec.yaml:** No extra dependency. Uses animation, theme, form.

**lib/main.dart - All-in-One Interactive Demo:**
```dart
import 'package:flutter/material.dart';

void main() => runApp(const InteractiveApp());

class InteractiveApp extends StatefulWidget {
  const InteractiveApp({super.key});
  @override
  State<InteractiveApp> createState() => _InteractiveAppState();
}

class _InteractiveAppState extends State<InteractiveApp> {
  bool _dark = false;
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Interactive App - VS Code',
      themeMode: _dark ? ThemeMode.dark : ThemeMode.light,
      theme: ThemeData(primarySwatch: Colors.indigo, useMaterial3: true),
      darkTheme: ThemeData.dark(useMaterial3: true),
      home: HomeScreen(
        dark: _dark,
        onTheme: (v) => setState(() => _dark = v),
      ),
      debugShowCheckedModeBanner: false,
    );
  }
}

class HomeScreen extends StatefulWidget {
  final bool dark;
  final ValueChanged<bool> onTheme;
  const HomeScreen({super.key, required this.dark, required this.onTheme});
  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> with SingleTickerProviderStateMixin {
  int _counter = 0;
  double _slider = 50;
  bool _liked = false;
  late AnimationController _ctrl;
  final _formKey = GlobalKey<FormState>();
  final _nameCtrl = TextEditingController();

  @override
  void initState() {
    super.initState();
    _ctrl = AnimationController(vsync: this, duration: const Duration(seconds: 2))..repeat(reverse: true);
  }

  @override
  void dispose() {
    _ctrl.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Interactive App (VS Code)'),
        actions: [
          Row(children: [
            const Icon(Icons.dark_mode),
            Switch(value: widget.dark, onChanged: widget.onTheme),
          ])
        ],
      ),
      body: SingleChildScrollView(
        padding: const EdgeInsets.all(16),
        child: Column(
          children: [
            // 1. Counter Interaction
            Card(
              child: Padding(
                padding: const EdgeInsets.all(16),
                child: Row(
                  mainAxisAlignment: MainAxisAlignment.spaceBetween,
                  children: [
                    const Text('Counter:', style: TextStyle(fontSize: 18)),
                    Row(children: [
                      IconButton(onPressed: () => setState(() => _counter--), icon: const Icon(Icons.remove)),
                      Text('$_counter', style: const TextStyle(fontSize: 24, fontWeight: FontWeight.bold)),
                      IconButton(onPressed: () => setState(() => _counter++), icon: const Icon(Icons.add)),
                    ]),
                  ],
                ),
              ),
            ),
            // 2. Slider + Animation
            Card(
              child: Padding(
                padding: const EdgeInsets.all(16),
                child: Column(children: [
                  Text('Slider Value: ${_slider.toInt()}'),
                  Slider(
                      value: _slider, min: 0, max: 100,
                      onChanged: (v) => setState(() => _slider = v)),
                  ScaleTransition(
                    scale: Tween(begin: 0.8, end: 1.2).animate(_ctrl),
                    child: Icon(Icons.favorite,
                        size: _slider, color: _liked ? Colors.red : Colors.grey),
                  ),
                  IconButton(
                      icon: Icon(_liked ? Icons.favorite : Icons.favorite_border,
                          color: Colors.red, size: 32),
                      onPressed: () => setState(() => _liked = !_liked)),
                ]),
              ),
            ),
            // 3. Form Validation
            Card(
              child: Padding(
                padding: const EdgeInsets.all(16),
                child: Form(
                  key: _formKey,
                  child: Column(children: [
                    const Text('Feedback Form', style: TextStyle(fontWeight: FontWeight.bold)),
                    TextFormField(
                      controller: _nameCtrl,
                      decoration: const InputDecoration(labelText: 'Your Name'),
                      validator: (v) => v!.isEmpty ? 'Enter name' : null,
                    ),
                    const SizedBox(height: 10),
                    ElevatedButton(
                      onPressed: () {
                        if (_formKey.currentState!.validate()) {
                          ScaffoldMessenger.of(context).showSnackBar(
                              SnackBar(content: Text('Thanks ${_nameCtrl.text}! Counter=$_counter')));
                        }
                      },
                      child: const Text('SUBMIT'),
                    ),
                  ]),
                ),
              ),
            ),
            const SizedBox(height: 10),
            const Text('Built with VS Code + Flutter - SDG 9: Innovation'),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
          onPressed: () => setState(() => _counter++),
          child: const Icon(Icons.touch_app)),
    );
  }
}
```

**VS Code launch.json (`.vscode/launch.json`):**
```json
{
  "version": "0.2.0",
  "configurations": [
    { "name": "Flutter", "request": "launch", "type": "dart" }
  ]
}
```

**Viva Points for all:**
- SDG 4: Login, College Portal, Quiz in Java improve Quality Education access.
- SDG 11: Geo-location in Java helps sustainable cities, navigation, emergency services.
- SDG 9: QR + Firebase in Java, Flutter interactive app promote innovation & infrastructure.

---
*Ex 10-14: Android Studio + Java. Ex 15: VS Code + Flutter. Each Java exercise = new Android project.*
