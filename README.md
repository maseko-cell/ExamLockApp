```mermaid
graph LR
    subgraph App[Android App]
        User((Siswa / Guru))
        Login[Login Screen]
        DashS[Dashboard Siswa]
        DashG[Dashboard Guru]
        GForm[Google Form WebView]
    end

    subgraph Backend[Google Cloud]
        GAS[Google Apps Script]
        Sheet[(Google Sheets)]
    end

    %% Alur Login
    User -->|Input Login| Login
    Login -->|POST action: login| GAS
    GAS -->|Cek Kredensial| Sheet
    Sheet -.->|Data User & Role| GAS
    GAS -->|JSON Response| Login

    %% Siswa Flow
    Login -->|Role: Siswa| DashS
    DashS -->|GET action: get_exams| GAS
    GAS -->|Filter Kelas & Status| Sheet
    GAS -->|Daftar Ujian| DashS
    
    DashS -->|Klik Mulai| GAS_B[POST action: begin_exam]
    GAS_B -->|Catat Sesi Mulai| Sheet
    
    DashS -->|Buka Link| GForm
    GForm -->|Submit Jawaban| Trigger[Trigger: onFormSubmit]
    Trigger -->|Otomatis| GAS_S[Fungsi onFormSubmit]
    GAS_S -->|Hitung & Cari Nama| Sheet
    GAS_S -->|Simpan ke Sheet Results| Sheet
    
    GForm -->|Selesai| GAS_E[POST action: end_exam_session]
    GAS_E -->|Update Status: Completed| Sheet

    %% Guru Flow
    Login -->|Role: Guru/Admin| DashG
    DashG -->|GET action: getAllResults| GAS
    GAS -->|Ambil Data Nilai| Sheet
    GAS -->|Daftar Nilai Siswa| DashG```
