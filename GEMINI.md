# GEMINI.md — Lakoni Project Brief

> File ini adalah konteks utama project Lakoni.
> Baca seluruh file ini sebelum menghasilkan kode apapun.

---

## 🧭 Tentang Lakoni

**Lakoni** adalah aplikasi career guidance berbasis AI untuk Gen-Z Indonesia.
Bukan sekadar quiz karir — Lakoni adalah "teman perjalanan" yang menemani user
dari fase SMA, kuliah, hingga dunia kerja, dengan pendekatan empatik dan konteks lokal.

**Tagline:** *Temukan arahmu. Mulai dari sekarang.*

---

## 🎯 Target User

| Segmen | Kondisi |
|--------|---------|
| 🏫 SMA / Mau Lulus | Bingung pilih jurusan atau jalur karir |
| 🎒 Mahasiswa | Merasa salah jurusan atau ketinggalan |
| 💼 Sedang Bekerja | Stagnan, stuck, mau naik level, ganti karir, atau mulai bisnis |

---

## 🛠️ Tech Stack

```
Frontend   : Next.js 14 (App Router) + TypeScript + Tailwind CSS
Backend    : Next.js API Routes (serverless)
Database   : Firebase Firestore
Auth       : Firebase Authentication (Google Sign-In)
AI         : Google AI Studio — Gemini 2.5 Flash (via @google/generative-ai SDK)
Hosting    : Firebase App Hosting / Cloud Run
```

> ⚠️ Jangan gunakan Supabase, Prisma, atau PostgreSQL.
> Semua data disimpan di **Firestore**, bukan SQL.

---

## 🗃️ Struktur Data (Firestore Collections)

```
/users/{userId}
  - email: string
  - name: string
  - createdAt: timestamp
  - lastActive: timestamp

/users/{userId}/profile
  - phase: "sma" | "kuliah" | "bekerja"
  - direction: "kuliah" | "kerja" | "bisnis"
  - interests: string[]
  - skills: string[]
  - economicStatus: "rendah" | "menengah" | "tinggi"
  - currentJob: string | null
  - updatedAt: timestamp

/users/{userId}/careerPaths/{pathId}
  - careerTitle: string
  - careerType: string
  - salaryMin: number
  - salaryMax: number
  - aiReasoning: string
  - isActive: boolean
  - createdAt: timestamp

/users/{userId}/careerPaths/{pathId}/roadmaps/{roadmapId}
  - phaseLabel: string
  - durationMonths: number
  - skillsNeeded: string[]
  - certifications: string[]
  - resources: { title: string, url: string }[]
  - orderIndex: number

/users/{userId}/milestones/{milestoneId}
  - roadmapId: string
  - title: string
  - status: "todo" | "doing" | "done"
  - completedAt: timestamp | null
  - notes: string

/users/{userId}/workCheckins/{checkinId}
  - condition: "stagnan" | "stuck" | "salah_pilih" | "naik_level" | "ganti_karir" | "bisnis"
  - durationMonths: number
  - currentPosition: string
  - aiRecommendation: string
  - actionPlan: { step: string, timeline: string }[]
  - checkedAt: timestamp

/users/{userId}/notifications/{notifId}
  - type: "reminder" | "antistuck" | "milestone" | "checkin"
  - message: string
  - isRead: boolean
  - scheduledAt: timestamp
  - sentAt: timestamp | null

/users/{userId}/aiSessions/{sessionId}
  - sessionType: "onboarding" | "checkin" | "work_checkin" | "chat"
  - messages: { role: "user" | "model", content: string }[]
  - aiOutput: string
  - createdAt: timestamp
```

---

## 🤖 AI Persona In-App: Lakon

- **Nama:** Lakon
- **Gaya:** Gen-Z Indonesia — santai, akrab, pakai "kamu/aku", sesekali pakai kata gaul yang wajar
- **Prinsip:** Validasi emosi dulu, baru kasih solusi
- **Bahasa:** Indonesia (bukan formal, bukan alay)
- **JANGAN:** Sok formal, pakai "Anda", atau terdengar seperti chatbot kaku

---

## 📁 Struktur Folder Project

```
/app
  /api
    /ai
      /onboarding/route.ts     ← P2: Analisis profil & rekomendasi awal
      /generate-roadmap/route.ts ← P3: Generate career roadmap
      /work-checkin/route.ts   ← P5: Analisis kondisi kerja
      /anti-stuck/route.ts     ← P6: Generate action plan anti-stuck
  /(auth)
    /login/page.tsx
    /register/page.tsx
  /(app)
    /onboarding/page.tsx
    /dashboard/page.tsx
    /roadmap/page.tsx
    /checkin/page.tsx
/components
  /ui/                         ← shadcn/ui components
  /lakon/                      ← Komponen chat & persona Lakon
  /roadmap/                    ← Visualisasi roadmap
/lib
  /firebase.ts                 ← Firebase config & init
  /gemini.ts                   ← Gemini client & helper functions
  /firestore.ts                ← Firestore CRUD helpers
/types
  /index.ts                    ← Semua TypeScript types
```

---

## ⚙️ Aturan Coding (WAJIB DIIKUTI)

1. **Selalu TypeScript** — tidak ada file `.js`, semua `.ts` / `.tsx`
2. **Semua Gemini output = JSON** yang langsung bisa disimpan ke Firestore
3. **Komentar dalam Bahasa Indonesia**
4. **Error handling** di setiap API route — jangan biarkan unhandled promise
5. **Jangan hardcode API key** — selalu dari `process.env`
6. **Komponen React = functional component** dengan hooks
7. **Setiap API route wajib validasi input** sebelum panggil Gemini

---

## 🔐 Environment Variables

```env
GEMINI_API_KEY=          # dari Google AI Studio
NEXT_PUBLIC_FIREBASE_API_KEY=
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=
NEXT_PUBLIC_FIREBASE_PROJECT_ID=
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=
NEXT_PUBLIC_FIREBASE_APP_ID=
```

---

## 🚫 Hal yang TIDAK Boleh Dilakukan

- Jangan gunakan `any` di TypeScript
- Jangan simpan data sensitif di localStorage
- Jangan panggil Gemini API langsung dari client/browser — selalu lewat API route
- Jangan buat UI dalam Bahasa Inggris — semua teks user-facing dalam Bahasa Indonesia
- Jangan skip loading state & error state di komponen

---

*File ini harus selalu ada di root project. Update jika ada perubahan arsitektur.*
