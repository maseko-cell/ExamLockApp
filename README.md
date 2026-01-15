```mermaid
graph LR
    subgraph App[Android App]
        User((Siswa / Guru / Admin))
        Login[Layar Login]
        DashS[Dashboard Siswa]
        DashG[Dashboard Guru]
        DashA[Dashboard Admin]
        GForm[Google Form WebView]
    end

    subgraph Backend[Google Cloud]
        GAS[Skrip Google Apps]
        Sheet[(Google Sheets)]
    end

    %% Alur Login
    User -->|Input Kredensial| Login
    Login -->|POST action: login| GAS
    GAS -->|Cek Role| Sheet
    GAS -.->|Response: Profil & Role| Login

    %% ALUR SISWA (Mengerjakan Ujian)
    Login -->|Role: Siswa| DashS
    DashS -->|Request Ujian| GAS
    GAS -->|Filter Kelas| Sheet
    GAS -.->|Daftar Ujian| DashS
    DashS -->|Klik Mulai| GAS
    GAS -->|Catat Sesi Mulai| Sheet
    DashS -->|Buka| GForm
    GForm -->|Submit Jawaban| GAS
    GAS -->|Fungsi onFormSubmit| Sheet
    
    %% ALUR GURU (Monitoring Hasil)
    Login -->|Role: Guru| DashG
    DashG -->|GET action: get_all_results| GAS
    GAS -->|Ambil Data Skor| Sheet
    GAS -.->|List Nilai Siswa| DashG

    %% ALUR ADMIN (Manajemen Sistem)
    Login -->|Role: Admin| DashA
    DashA -->|action: get_all_exams| GAS
    DashA -->|action: get_all_users| GAS
    DashA -->|action: create_exam| GAS
    GAS <-->|CRUD Data Master| Sheet
    GAS -.->|Data Exams/Users| DashA

    %% Hubungan Sesi
    GForm -->|Selesai| GAS
    GAS -->|Update Status: Completed| Sheet```
