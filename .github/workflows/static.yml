import React, { useState } from "react";
import {
  ClipboardList, Calendar, BarChart3, Plus, ArrowLeft, CheckCircle2,
  Circle, Trash2, Pencil, Flag, X, BookOpen, AlarmClock, LogOut,
  GraduationCap, UserRound, Send, FileCheck2, Users, Lock, ChevronRight,
} from "lucide-react";

/* ============================================================
   TugasKu — manajemen tugas guru & siswa
   Guru   : membuat tugas, memantau pengumpulan siswa.
   Siswa  : melihat tugas dari guru, mengumpulkan tugas.
   ============================================================ */

const INK = "#201E33";
const MUTED = "#8583A0";
const BG = "#F5F4FB";
const PRIMARY = "#5750E0";
const PRIMARY_BG = "#EBEAFB";
const TEAL = "#0EA5A0";
const TEAL_BG = "#E3F7F6";

const PRIORITAS = {
  Tinggi: "#E5484D",
  Sedang: "#F2A93B",
  Rendah: "#34B27B",
};

const DAY_MS = 24 * 60 * 60 * 1000;
const fmtISO = (d) => d.toISOString().slice(0, 10);
const addDays = (n) => fmtISO(new Date(Date.now() + n * DAY_MS));
const today = new Date(); today.setHours(0, 0, 0, 0);

function daysLeft(iso) {
  const d = new Date(iso + "T00:00:00");
  return Math.round((d - today) / DAY_MS);
}
function reminderText(iso, status) {
  if (status === "selesai") return null;
  const n = daysLeft(iso);
  if (n < 0) return { text: `Terlambat ${Math.abs(n)} hari`, tone: "danger" };
  if (n === 0) return { text: "Deadline hari ini", tone: "danger" };
  if (n === 1) return { text: "Besok deadline", tone: "warn" };
  if (n <= 7) return { text: `H-${n}`, tone: "info" };
  return { text: `H-${n}`, tone: "muted" };
}
const TONE_COLOR = { danger: "#E5484D", warn: "#F2A93B", info: PRIMARY, muted: MUTED };

const HARI = ["Min", "Sen", "Sel", "Rab", "Kam", "Jum", "Sab"];
const BULAN = ["Januari", "Februari", "Maret", "April", "Mei", "Juni", "Juli", "Agustus", "September", "Oktober", "November", "Desember"];
function fmtLong(iso) {
  const d = new Date(iso + "T00:00:00");
  return `${d.getDate()} ${BULAN[d.getMonth()]} ${d.getFullYear()}`;
}
const uid = () => Math.floor(Math.random() * 1e9);

/* ------------------------ akun contoh ------------------------ */
const AKUN_GURU = [
  { username: "guru.sari", password: "guru123", nama: "Bu Sari Wulandari" },
];
const AKUN_SISWA = [
  { username: "gali", password: "siswa123", nama: "Gali Pratama", kelas: "9A" },
  { username: "nadia", password: "siswa123", nama: "Nadia Putri", kelas: "9A" },
];

/* ------------------------ data awal tugas ---------------------- */
const TUGAS_AWAL = [
  { id: 1, nama: "Latihan soal integral", mapel: "Matematika", deadline: addDays(-1), prioritas: "Tinggi", catatan: "", dibuatOleh: "guru.sari", pengumpulan: {} },
  { id: 2, nama: "Rangkuman bab Perang Dunia II", mapel: "Sejarah", deadline: addDays(0), prioritas: "Sedang", catatan: "", dibuatOleh: "guru.sari", pengumpulan: {} },
  { id: 3, nama: "Laporan praktikum sel", mapel: "Biologi", deadline: addDays(1), prioritas: "Tinggi", catatan: "", dibuatOleh: "guru.sari", pengumpulan: {} },
  { id: 4, nama: "Essay descriptive text", mapel: "Bahasa Inggris", deadline: addDays(3), prioritas: "Sedang", catatan: "", dibuatOleh: "guru.sari", pengumpulan: {
    gali: { teks: "Sudah saya kerjakan, terlampir draf essay saya.", waktu: addDays(-1) },
  } },
  { id: 5, nama: "Poster kampanye lingkungan", mapel: "Seni Budaya", deadline: addDays(6), prioritas: "Rendah", catatan: "", dibuatOleh: "guru.sari", pengumpulan: {} },
];

export default function TugasKuApp() {
  const [akun, setAkun] = useState(null); // { peran: 'guru'|'siswa', username, nama, kelas? }
  const [tugasList, setTugasList] = useState(TUGAS_AWAL);

  if (!akun) {
    return (
      <PhoneShell>
        <LoginScreen onLogin={setAkun} />
      </PhoneShell>
    );
  }

  return (
    <PhoneShell>
      {akun.peran === "guru" ? (
        <GuruApp akun={akun} onLogout={() => setAkun(null)} tugasList={tugasList} setTugasList={setTugasList} />
      ) : (
        <SiswaApp akun={akun} onLogout={() => setAkun(null)} tugasList={tugasList} setTugasList={setTugasList} />
      )}
    </PhoneShell>
  );
}

/* ============================================================ */
function PhoneShell({ children }) {
  return (
    <div className="w-full min-h-[640px] flex items-center justify-center py-6" style={{ background: "linear-gradient(180deg,#EDEBFA,#DCD9F5)", fontFamily: "'Inter',sans-serif" }}>
      <style>{`@import url('https://fonts.googleapis.com/css2?family=Sora:wght@600;700;800&family=Inter:wght@400;500;600;700&display=swap');`}</style>
      <div className="w-[380px] h-[720px] rounded-[2.2rem] overflow-hidden shadow-2xl border-[6px] border-slate-900 bg-white relative">
        {children}
      </div>
    </div>
  );
}

function Card({ children, className = "" }) {
  return <div className={`bg-white rounded-2xl p-4 border border-slate-100 shadow-sm ${className}`}>{children}</div>;
}

/* --------------------------- LOGIN --------------------------- */
function LoginScreen({ onLogin }) {
  const [peran, setPeran] = useState("siswa"); // 'siswa' | 'guru'
  const [username, setUsername] = useState("");
  const [password, setPassword] = useState("");
  const [error, setError] = useState("");

  function handleSubmit() {
    const daftar = peran === "guru" ? AKUN_GURU : AKUN_SISWA;
    const cocok = daftar.find((a) => a.username === username.trim().toLowerCase() && a.password === password);
    if (!cocok) {
      setError(peran === "guru" ? "Username atau kata sandi guru salah." : "Username atau kata sandi siswa salah.");
      return;
    }
    setError("");
    onLogin({ peran, username: cocok.username, nama: cocok.nama, kelas: cocok.kelas });
  }

  function isiContoh() {
    if (peran === "guru") { setUsername("guru.sari"); setPassword("guru123"); }
    else { setUsername("gali"); setPassword("siswa123"); }
    setError("");
  }

  const inputCls = "w-full border border-slate-200 rounded-lg px-3 py-2.5 text-sm outline-none focus:border-slate-400";

  return (
    <div className="h-full flex flex-col" style={{ background: BG }}>
      <div className="px-6 pt-10 pb-8" style={{ background: PRIMARY }}>
        <div className="w-12 h-12 rounded-2xl bg-white/15 flex items-center justify-center mb-3">
          <ClipboardList size={24} color="#fff" />
        </div>
        <p className="text-white font-bold text-2xl" style={{ fontFamily: "'Sora',sans-serif" }}>TugasKu</p>
        <p className="text-white/70 text-xs mt-1">Kelola dan kumpulkan tugas sekolah dalam satu tempat.</p>
      </div>

      <div className="flex-1 px-6 pt-6">
        <div className="flex gap-2 mb-5 p-1 rounded-xl" style={{ background: "#EFEEFA" }}>
          <button
            onClick={() => { setPeran("siswa"); setError(""); }}
            className="flex-1 py-2.5 rounded-lg text-xs font-semibold flex items-center justify-center gap-1.5"
            style={peran === "siswa" ? { background: "#fff", color: PRIMARY, boxShadow: "0 1px 3px rgba(0,0,0,0.08)" } : { color: MUTED }}
          >
            <UserRound size={14} /> Siswa
          </button>
          <button
            onClick={() => { setPeran("guru"); setError(""); }}
            className="flex-1 py-2.5 rounded-lg text-xs font-semibold flex items-center justify-center gap-1.5"
            style={peran === "guru" ? { background: "#fff", color: PRIMARY, boxShadow: "0 1px 3px rgba(0,0,0,0.08)" } : { color: MUTED }}
          >
            <GraduationCap size={14} /> Guru
          </button>
        </div>

        <div className="mb-3">
          <label className="text-xs font-medium mb-1 block" style={{ color: MUTED }}>Username</label>
          <input value={username} onChange={(e) => setUsername(e.target.value)} placeholder={peran === "guru" ? "mis. guru.sari" : "mis. gali"} className={inputCls} />
        </div>
        <div className="mb-2">
          <label className="text-xs font-medium mb-1 block" style={{ color: MUTED }}>Kata Sandi</label>
          <div className="relative">
            <input type="password" value={password} onChange={(e) => setPassword(e.target.value)} placeholder="••••••••" className={inputCls} />
            <Lock size={14} color="#C9C7DE" className="absolute right-3 top-1/2 -translate-y-1/2" />
          </div>
        </div>

        {error && <p className="text-[11px] mb-2" style={{ color: "#E5484D" }}>{error}</p>}

        <button onClick={handleSubmit} disabled={!username.trim() || !password}
          className="w-full py-2.5 rounded-lg text-sm font-semibold text-white disabled:opacity-40 mt-3"
          style={{ background: PRIMARY }}>
          Masuk sebagai {peran === "guru" ? "Guru" : "Siswa"}
        </button>

        <button onClick={isiContoh} className="w-full text-center text-[11px] mt-3" style={{ color: PRIMARY }}>
          Isi otomatis akun contoh
        </button>
      </div>

      <p className="text-center text-[10px] pb-5" style={{ color: MUTED }}>
        Akun contoh — guru.sari / guru123 · gali / siswa123
      </p>
    </div>
  );
}

/* ============================================================ */
/* ============================ GURU ============================= */
function GuruApp({ akun, onLogout, tugasList, setTugasList }) {
  const [screen, setScreen] = useState("list");
  const [filter, setFilter] = useState("semua");
  const [editId, setEditId] = useState(null);
  const [detailId, setDetailId] = useState(null);
  const [calMonth, setCalMonth] = useState(new Date(today.getFullYear(), today.getMonth(), 1));
  const [selectedDate, setSelectedDate] = useState(null);

  const milikGuru = tugasList.filter((t) => t.dibuatOleh === akun.username);
  const totalSiswa = AKUN_SISWA.length;

  function openAdd() { setEditId(null); setScreen("tambah"); }
  function openEdit(id) { setEditId(id); setScreen("tambah"); }
  function openDetail(id) { setDetailId(id); setScreen("detail"); }
  function removeTugas(id) { setTugasList((l) => l.filter((t) => t.id !== id)); }
  function saveTugas(data) {
    if (editId) setTugasList((l) => l.map((t) => t.id === editId ? { ...t, ...data } : t));
    else setTugasList((l) => [...l, { id: uid(), dibuatOleh: akun.username, pengumpulan: {}, ...data }]);
    setScreen("list");
  }

  const titles = {
    list: "TugasKu · Guru", tambah: editId ? "Edit Tugas" : "Tambah Tugas",
    detail: "Pengumpulan", kalender: "Kalender Tugas", statistik: "Statistik",
  };
  const isSub = screen === "tambah" || screen === "detail";

  return (
    <div className="h-full flex flex-col" style={{ background: BG }}>
      <TopBar
        title={titles[screen]}
        subtitle={screen === "list" ? `Halo, ${akun.nama}` : undefined}
        isSub={isSub}
        onBack={() => setScreen("list")}
        onLogout={onLogout}
      />
      <div className="flex-1 overflow-y-auto px-4 py-4 relative">
        {screen === "list" && (
          <GuruListScreen
            tugasList={milikGuru} filter={filter} setFilter={setFilter}
            onEdit={openEdit} onDelete={removeTugas} onOpenDetail={openDetail}
            totalSiswa={totalSiswa}
          />
        )}
        {screen === "tambah" && (
          <FormScreen
            initial={editId ? milikGuru.find((t) => t.id === editId) : null}
            onCancel={() => setScreen("list")} onSave={saveTugas}
          />
        )}
        {screen === "detail" && (
          <DetailPengumpulanScreen
            tugas={tugasList.find((t) => t.id === detailId)}
            siswaList={AKUN_SISWA}
          />
        )}
        {screen === "kalender" && (
          <KalenderScreen
            tugasList={milikGuru} calMonth={calMonth} setCalMonth={setCalMonth}
            selectedDate={selectedDate} setSelectedDate={setSelectedDate}
            onSelectTugas={openDetail}
            mode="guru"
          />
        )}
        {screen === "statistik" && <StatistikGuruScreen tugasList={milikGuru} totalSiswa={totalSiswa} />}
      </div>
      {screen === "list" && (
        <button onClick={openAdd} className="absolute right-5 shadow-lg flex items-center justify-center"
          style={{ bottom: 78, width: 52, height: 52, borderRadius: "50%", background: PRIMARY, color: "#fff" }}>
          <Plus size={24} />
        </button>
      )}
      <BottomNav screen={screen} setScreen={setScreen} tabs={[
        ["list", ClipboardList, "Tugas"], ["kalender", Calendar, "Kalender"], ["statistik", BarChart3, "Statistik"],
      ]} />
    </div>
  );
}

function GuruListScreen({ tugasList, filter, setFilter, onEdit, onDelete, onOpenDetail, totalSiswa }) {
  const withCount = tugasList.map((t) => ({ ...t, jmlKumpul: Object.keys(t.pengumpulan || {}).length }));
  const filtered = withCount
    .filter((t) => filter === "semua" ? true : filter === "belum" ? t.jmlKumpul < totalSiswa : t.jmlKumpul >= totalSiswa)
    .sort((a, b) => a.deadline.localeCompare(b.deadline));

  return (
    <div className="space-y-3 pb-16">
      <div className="flex gap-2">
        {[["semua", "Semua"], ["belum", "Berjalan"], ["selesai", "Terkumpul semua"]].map(([k, l]) => (
          <button key={k} onClick={() => setFilter(k)}
            className="px-3 py-1.5 rounded-full text-xs font-semibold"
            style={filter === k ? { background: PRIMARY, color: "#fff" } : { background: "#EFEEFA", color: MUTED }}>
            {l}
          </button>
        ))}
      </div>

      {filtered.length === 0 && (
        <Card className="text-center py-8"><p className="text-sm" style={{ color: MUTED }}>Belum ada tugas di sini.</p></Card>
      )}

      {filtered.map((t) => {
        const rem = reminderText(t.deadline, "belum");
        return (
          <Card key={t.id}>
            <div className="flex items-start gap-3">
              <div className="flex-1 min-w-0" onClick={() => onOpenDetail(t.id)}>
                <p className="text-sm font-semibold" style={{ color: INK }}>{t.nama}</p>
                <div className="flex items-center gap-2 mt-1 flex-wrap">
                  <span className="text-[11px] px-2 py-0.5 rounded-full flex items-center gap-1" style={{ background: PRIMARY_BG, color: PRIMARY }}>
                    <BookOpen size={10} /> {t.mapel}
                  </span>
                  <span className="text-[11px] px-2 py-0.5 rounded-full flex items-center gap-1" style={{ background: `${PRIORITAS[t.prioritas]}1A`, color: PRIORITAS[t.prioritas] }}>
                    <Flag size={10} /> {t.prioritas}
                  </span>
                </div>
                <div className="flex items-center justify-between mt-2">
                  <span className="text-[11px]" style={{ color: MUTED }}>{fmtLong(t.deadline)}</span>
                  {rem && (
                    <span className="text-[11px] font-semibold flex items-center gap-1" style={{ color: TONE_COLOR[rem.tone] }}>
                      <AlarmClock size={11} /> {rem.text}
                    </span>
                  )}
                </div>
              </div>
              <div className="flex flex-col gap-2 shrink-0">
                <button onClick={() => onEdit(t.id)}><Pencil size={14} color="#B4B2C7" /></button>
                <button onClick={() => onDelete(t.id)}><Trash2 size={14} color="#E5484D" /></button>
              </div>
            </div>
            <button onClick={() => onOpenDetail(t.id)}
              className="w-full mt-3 pt-3 border-t border-slate-100 flex items-center justify-between">
              <span className="text-[11px] font-semibold flex items-center gap-1.5" style={{ color: TEAL }}>
                <Users size={12} /> {t.jmlKumpul}/{totalSiswa} siswa mengumpulkan
              </span>
              <ChevronRight size={14} color="#B4B2C7" />
            </button>
          </Card>
        );
      })}
    </div>
  );
}

function DetailPengumpulanScreen({ tugas, siswaList }) {
  if (!tugas) return <Card className="text-center py-8"><p className="text-sm" style={{ color: MUTED }}>Tugas tidak ditemukan.</p></Card>;
  const pengumpulan = tugas.pengumpulan || {};

  return (
    <div className="space-y-3 pb-4">
      <Card>
        <p className="text-sm font-semibold" style={{ color: INK }}>{tugas.nama}</p>
        <div className="flex items-center gap-2 mt-1.5 flex-wrap">
          <span className="text-[11px] px-2 py-0.5 rounded-full flex items-center gap-1" style={{ background: PRIMARY_BG, color: PRIMARY }}>
            <BookOpen size={10} /> {tugas.mapel}
          </span>
          <span className="text-[11px]" style={{ color: MUTED }}>Deadline {fmtLong(tugas.deadline)}</span>
        </div>
      </Card>

      <p className="text-xs font-semibold px-1" style={{ color: MUTED }}>
        {Object.keys(pengumpulan).length} dari {siswaList.length} siswa sudah mengumpulkan
      </p>

      {siswaList.map((s) => {
        const kumpul = pengumpulan[s.username];
        return (
          <Card key={s.username}>
            <div className="flex items-start gap-3">
              {kumpul ? <FileCheck2 size={18} color={TEAL} className="mt-0.5 shrink-0" /> : <Circle size={18} color="#C9C7DE" className="mt-0.5 shrink-0" />}
              <div className="flex-1 min-w-0">
                <p className="text-sm font-semibold" style={{ color: INK }}>{s.nama}</p>
                <p className="text-[11px]" style={{ color: MUTED }}>Kelas {s.kelas}</p>
                {kumpul ? (
                  <div className="mt-2 p-2.5 rounded-lg" style={{ background: TEAL_BG }}>
                    <p className="text-xs" style={{ color: INK }}>{kumpul.teks}</p>
                    <p className="text-[10px] mt-1" style={{ color: MUTED }}>Dikumpulkan {fmtLong(kumpul.waktu)}</p>
                  </div>
                ) : (
                  <p className="text-[11px] mt-1.5 font-medium" style={{ color: "#E5484D" }}>Belum mengumpulkan</p>
                )}
              </div>
            </div>
          </Card>
        );
      })}
    </div>
  );
}

function StatistikGuruScreen({ tugasList, totalSiswa }) {
  const totalTugas = tugasList.length;
  const totalSlot = totalTugas * totalSiswa;
  const totalKumpul = tugasList.reduce((sum, t) => sum + Object.keys(t.pengumpulan || {}).length, 0);
  const pct = totalSlot ? Math.round((totalKumpul / totalSlot) * 100) : 0;
  const r = 42, c = 2 * Math.PI * r;

  const perMapel = {};
  tugasList.forEach((t) => { perMapel[t.mapel] = (perMapel[t.mapel] || 0) + 1; });

  return (
    <div className="space-y-3 pb-4">
      <Card className="flex flex-col items-center py-6">
        <svg width="110" height="110" viewBox="0 0 100 100">
          <circle cx="50" cy="50" r={r} fill="none" stroke="#EFEEFA" strokeWidth="9" />
          <circle cx="50" cy="50" r={r} fill="none" stroke={TEAL} strokeWidth="9" strokeLinecap="round"
            strokeDasharray={c} strokeDashoffset={c - (pct / 100) * c} transform="rotate(-90 50 50)" />
          <text x="50" y="55" textAnchor="middle" fontSize="20" fontWeight="700" fill={INK}>{pct}%</text>
        </svg>
        <p className="text-xs mt-2" style={{ color: MUTED }}>Rata-rata tingkat pengumpulan siswa</p>
      </Card>

      <div className="grid grid-cols-2 gap-3">
        <Card className="text-center"><p className="text-2xl font-extrabold" style={{ color: PRIMARY, fontFamily: "'Sora',sans-serif" }}>{totalTugas}</p><p className="text-[11px]" style={{ color: MUTED }}>Tugas Dibuat</p></Card>
        <Card className="text-center"><p className="text-2xl font-extrabold" style={{ color: TEAL, fontFamily: "'Sora',sans-serif" }}>{totalKumpul}/{totalSlot}</p><p className="text-[11px]" style={{ color: MUTED }}>Total Pengumpulan</p></Card>
      </div>

      <Card>
        <p className="text-xs font-semibold mb-3" style={{ color: MUTED }}>Tugas per mata pelajaran</p>
        {Object.entries(perMapel).map(([m, n]) => (
          <div key={m} className="flex justify-between items-center py-1.5 text-sm">
            <span style={{ color: INK }}>{m}</span>
            <span className="px-2 py-0.5 rounded-full text-xs font-semibold" style={{ background: PRIMARY_BG, color: PRIMARY }}>{n}</span>
          </div>
        ))}
        {totalTugas === 0 && <p className="text-xs" style={{ color: MUTED }}>Belum ada tugas.</p>}
      </Card>
    </div>
  );
}

/* ------------------------- TOP BAR & NAV ----------------------- */
function TopBar({ title, subtitle, isSub, onBack, onLogout, accent = PRIMARY }) {
  return (
    <div className="px-4 pt-5 pb-4 flex items-center justify-between" style={{ background: accent }}>
      <div className="flex items-center gap-3 min-w-0">
        {isSub ? (
          <button onClick={onBack} className="text-white shrink-0"><ArrowLeft size={20} /></button>
        ) : (
          <ClipboardList size={20} color="#fff" className="shrink-0" />
        )}
        <div className="min-w-0">
          <p className="text-white font-bold text-base truncate" style={{ fontFamily: "'Sora',sans-serif" }}>{title}</p>
          {subtitle && <p className="text-white/70 text-[11px] truncate">{subtitle}</p>}
        </div>
      </div>
      {!isSub && onLogout && (
        <button onClick={onLogout} className="text-white/80 shrink-0 ml-2"><LogOut size={18} /></button>
      )}
    </div>
  );
}

/* ============================================================ */
/* =========================== SISWA ============================= */
function SiswaApp({ akun, onLogout, tugasList, setTugasList }) {
  const [screen, setScreen] = useState("list");
  const [filter, setFilter] = useState("semua");
  const [detailId, setDetailId] = useState(null);
  const [calMonth, setCalMonth] = useState(new Date(today.getFullYear(), today.getMonth(), 1));
  const [selectedDate, setSelectedDate] = useState(null);

  const withStatus = tugasList.map((t) => ({
    ...t,
    status: t.pengumpulan && t.pengumpulan[akun.username] ? "selesai" : "belum",
  }));

  function openDetail(id) { setDetailId(id); setScreen("detail"); }
  function kumpulkanTugas(id, teks) {
    setTugasList((l) => l.map((t) => t.id === id
      ? { ...t, pengumpulan: { ...t.pengumpulan, [akun.username]: { teks, waktu: fmtISO(new Date()) } } }
      : t));
    setScreen("list");
  }
  function batalkanPengumpulan(id) {
    setTugasList((l) => l.map((t) => {
      if (t.id !== id) return t;
      const sisa = { ...t.pengumpulan };
      delete sisa[akun.username];
      return { ...t, pengumpulan: sisa };
    }));
  }

  const belum = withStatus.filter((t) => t.status === "belum").length;
  const titles = { list: "TugasKu · Siswa", detail: "Kumpulkan Tugas", kalender: "Kalender Tugas", statistik: "Statistik" };
  const isSub = screen === "detail";

  return (
    <div className="h-full flex flex-col" style={{ background: BG }}>
      <TopBar
        title={titles[screen]}
        subtitle={screen === "list" ? `${akun.nama} · Kelas ${akun.kelas}` : undefined}
        isSub={isSub}
        onBack={() => setScreen("list")}
        onLogout={onLogout}
        accent={TEAL}
      />
      <div className="flex-1 overflow-y-auto px-4 py-4 relative">
        {screen === "list" && (
          <SiswaListScreen tugasList={withStatus} filter={filter} setFilter={setFilter} onOpen={openDetail} />
        )}
        {screen === "detail" && (
          <SiswaDetailScreen
            tugas={withStatus.find((t) => t.id === detailId)}
            akun={akun}
            onKumpulkan={kumpulkanTugas}
            onBatalkan={batalkanPengumpulan}
          />
        )}
        {screen === "kalender" && (
          <KalenderScreen
            tugasList={withStatus} calMonth={calMonth} setCalMonth={setCalMonth}
            selectedDate={selectedDate} setSelectedDate={setSelectedDate}
            onSelectTugas={openDetail}
            mode="siswa"
          />
        )}
        {screen === "statistik" && <StatistikScreen tugasList={withStatus} accent={TEAL} />}
      </div>
      <BottomNav screen={screen} setScreen={setScreen} accent={TEAL} tabs={[
        ["list", ClipboardList, "Tugas"], ["kalender", Calendar, "Kalender"], ["statistik", BarChart3, "Statistik"],
      ]} />
    </div>
  );
}

function SiswaListScreen({ tugasList, filter, setFilter, onOpen }) {
  const filtered = tugasList.filter((t) => filter === "semua" ? true : t.status === filter)
    .sort((a, b) => a.deadline.localeCompare(b.deadline));

  return (
    <div className="space-y-3 pb-4">
      <div className="flex gap-2">
        {[["semua", "Semua"], ["belum", "Belum"], ["selesai", "Terkumpul"]].map(([k, l]) => (
          <button key={k} onClick={() => setFilter(k)}
            className="px-3 py-1.5 rounded-full text-xs font-semibold"
            style={filter === k ? { background: TEAL, color: "#fff" } : { background: "#EFEEFA", color: MUTED }}>
            {l}
          </button>
        ))}
      </div>

      {filtered.length === 0 && (
        <Card className="text-center py-8"><p className="text-sm" style={{ color: MUTED }}>Tidak ada tugas di sini.</p></Card>
      )}

      {filtered.map((t) => {
        const rem = reminderText(t.deadline, t.status);
        const done = t.status === "selesai";
        return (
          <button key={t.id} onClick={() => onOpen(t.id)} className="w-full text-left">
            <Card className={done ? "opacity-70" : ""}>
              <div className="flex items-start gap-3">
                {done ? <CheckCircle2 size={20} color={TEAL} className="mt-0.5 shrink-0" /> : <Circle size={20} color="#C9C7DE" className="mt-0.5 shrink-0" />}
                <div className="flex-1 min-w-0">
                  <p className="text-sm font-semibold" style={{ color: INK, textDecoration: done ? "line-through" : "none" }}>{t.nama}</p>
                  <div className="flex items-center gap-2 mt-1 flex-wrap">
                    <span className="text-[11px] px-2 py-0.5 rounded-full flex items-center gap-1" style={{ background: PRIMARY_BG, color: PRIMARY }}>
                      <BookOpen size={10} /> {t.mapel}
                    </span>
                    <span className="text-[11px] px-2 py-0.5 rounded-full flex items-center gap-1" style={{ background: `${PRIORITAS[t.prioritas]}1A`, color: PRIORITAS[t.prioritas] }}>
                      <Flag size={10} /> {t.prioritas}
                    </span>
                  </div>
                  <div className="flex items-center justify-between mt-2">
                    <span className="text-[11px]" style={{ color: MUTED }}>{fmtLong(t.deadline)}</span>
                    {rem && (
                      <span className="text-[11px] font-semibold flex items-center gap-1" style={{ color: TONE_COLOR[rem.tone] }}>
                        <AlarmClock size={11} /> {rem.text}
                      </span>
                    )}
                    {done && (
                      <span className="text-[11px] font-semibold flex items-center gap-1" style={{ color: TEAL }}>
                        <FileCheck2 size={11} /> Terkumpul
                      </span>
                    )}
                  </div>
                </div>
                <ChevronRight size={16} color="#B4B2C7" className="shrink-0 mt-1" />
              </div>
            </Card>
          </button>
        );
      })}
    </div>
  );
}

function SiswaDetailScreen({ tugas, akun, onKumpulkan, onBatalkan }) {
  const [teks, setTeks] = useState("");
  if (!tugas) return <Card className="text-center py-8"><p className="text-sm" style={{ color: MUTED }}>Tugas tidak ditemukan.</p></Card>;

  const sudah = tugas.pengumpulan && tugas.pengumpulan[akun.username];
  const rem = reminderText(tugas.deadline, tugas.status);

  return (
    <div className="space-y-3 pb-4">
      <Card>
        <p className="text-sm font-semibold" style={{ color: INK }}>{tugas.nama}</p>
        <div className="flex items-center gap-2 mt-1.5 flex-wrap">
          <span className="text-[11px] px-2 py-0.5 rounded-full flex items-center gap-1" style={{ background: PRIMARY_BG, color: PRIMARY }}>
            <BookOpen size={10} /> {tugas.mapel}
          </span>
          <span className="text-[11px] px-2 py-0.5 rounded-full flex items-center gap-1" style={{ background: `${PRIORITAS[tugas.prioritas]}1A`, color: PRIORITAS[tugas.prioritas] }}>
            <Flag size={10} /> {tugas.prioritas}
          </span>
        </div>
        <div className="flex items-center justify-between mt-3 pt-3 border-t border-slate-100">
          <span className="text-xs" style={{ color: MUTED }}>Deadline {fmtLong(tugas.deadline)}</span>
          {rem && <span className="text-[11px] font-semibold flex items-center gap-1" style={{ color: TONE_COLOR[rem.tone] }}><AlarmClock size={11} /> {rem.text}</span>}
        </div>
        {tugas.catatan && <p className="text-xs mt-2 pt-2 border-t border-slate-100" style={{ color: INK }}>{tugas.catatan}</p>}
      </Card>

      {sudah ? (
        <Card>
          <div className="flex items-center gap-2 mb-2">
            <FileCheck2 size={16} color={TEAL} />
            <p className="text-sm font-semibold" style={{ color: INK }}>Sudah kamu kumpulkan</p>
          </div>
          <div className="p-2.5 rounded-lg mb-3" style={{ background: TEAL_BG }}>
            <p className="text-xs" style={{ color: INK }}>{sudah.teks}</p>
            <p className="text-[10px] mt-1" style={{ color: MUTED }}>Dikumpulkan {fmtLong(sudah.waktu)}</p>
          </div>
          <button onClick={() => onBatalkan(tugas.id)} className="w-full py-2 rounded-lg text-xs font-semibold border border-slate-200" style={{ color: MUTED }}>
            Batalkan pengumpulan
          </button>
        </Card>
      ) : (
        <Card>
          <p className="text-sm font-semibold mb-2" style={{ color: INK }}>Kumpulkan tugas</p>
          <textarea value={teks} onChange={(e) => setTeks(e.target.value)} rows={4}
            placeholder="Tulis jawaban, ringkasan, atau tautan file tugasmu di sini..."
            className="w-full border border-slate-200 rounded-lg px-3 py-2 text-sm outline-none focus:border-slate-400" />
          <button onClick={() => teks.trim() && onKumpulkan(tugas.id, teks.trim())}
            disabled={!teks.trim()}
            className="w-full mt-3 py-2.5 rounded-lg text-sm font-semibold text-white disabled:opacity-40 flex items-center justify-center gap-2"
            style={{ background: TEAL }}>
            <Send size={14} /> Kumpulkan Tugas
          </button>
        </Card>
      )}
    </div>
  );
}

function BottomNav({ screen, setScreen, tabs, accent = PRIMARY }) {
  return (
    <div className="flex border-t border-slate-200 bg-white relative z-10">
      {tabs.map(([key, Icon, label]) => {
        const active = screen === key || (screen === "tambah" && key === "list") || (screen === "detail" && key === "list");
        return (
          <button key={key} onClick={() => setScreen(key)} className="flex-1 py-2.5 flex flex-col items-center gap-1">
            <Icon size={18} color={active ? accent : "#B4B2C7"} />
            <span className="text-[10px] font-medium" style={{ color: active ? accent : "#B4B2C7" }}>{label}</span>
          </button>
        );
      })}
    </div>
  );
}

/* ------------------------ FORM (guru: tambah/edit) -------------------*/
function FormScreen({ initial, onCancel, onSave }) {
  const [nama, setNama] = useState(initial?.nama || "");
  const [mapel, setMapel] = useState(initial?.mapel || "");
  const [deadline, setDeadline] = useState(initial?.deadline || addDays(1));
  const [prioritas, setPrioritas] = useState(initial?.prioritas || "Sedang");
  const [catatan, setCatatan] = useState(initial?.catatan || "");

  const inputCls = "w-full border border-slate-200 rounded-lg px-3 py-2 text-sm outline-none focus:border-slate-400";

  return (
    <Card>
      <Field label="Nama Tugas">
        <input value={nama} onChange={(e) => setNama(e.target.value)} placeholder="mis. Latihan soal integral" className={inputCls} />
      </Field>
      <Field label="Mata Pelajaran">
        <input value={mapel} onChange={(e) => setMapel(e.target.value)} placeholder="mis. Matematika" className={inputCls} />
      </Field>
      <Field label="Deadline">
        <input type="date" value={deadline} onChange={(e) => setDeadline(e.target.value)} className={inputCls} />
      </Field>
      <Field label="Prioritas">
        <div className="flex gap-2">
          {Object.keys(PRIORITAS).map((p) => (
            <button key={p} onClick={() => setPrioritas(p)} className="flex-1 py-2 rounded-lg text-xs font-semibold border"
              style={prioritas === p ? { background: PRIORITAS[p], borderColor: PRIORITAS[p], color: "#fff" } : { borderColor: "#E2E8F0", color: MUTED }}>
              {p}
            </button>
          ))}
        </div>
      </Field>
      <Field label="Instruksi (opsional)">
        <textarea value={catatan} onChange={(e) => setCatatan(e.target.value)} rows={3} placeholder="Detail atau instruksi untuk siswa..." className={inputCls} />
      </Field>
      <div className="flex gap-2 mt-2">
        <button
          onClick={() => nama.trim() && mapel.trim() && onSave({ nama, mapel, deadline, prioritas, catatan })}
          disabled={!nama.trim() || !mapel.trim()}
          className="flex-1 py-2.5 rounded-lg text-sm font-semibold text-white disabled:opacity-40"
          style={{ background: PRIMARY }}
        >
          Simpan
        </button>
        <button onClick={onCancel} className="flex-1 py-2.5 rounded-lg text-sm font-semibold border border-slate-200" style={{ color: MUTED }}>
          Batal
        </button>
      </div>
    </Card>
  );
}
function Field({ label, children }) {
  return <div className="mb-3"><label className="text-xs font-medium mb-1 block" style={{ color: MUTED }}>{label}</label>{children}</div>;
}

/* --------------------------- KALENDER (guru & siswa) --------------------------*/
function KalenderScreen({ tugasList, calMonth, setCalMonth, selectedDate, setSelectedDate, onSelectTugas, mode }) {
  const accent = mode === "siswa" ? TEAL : PRIMARY;
  const year = calMonth.getFullYear(), month = calMonth.getMonth();
  const firstDay = new Date(year, month, 1).getDay();
  const daysInMonth = new Date(year, month + 1, 0).getDate();
  const cells = [...Array(firstDay).fill(null), ...Array.from({ length: daysInMonth }, (_, i) => i + 1)];

  const tasksByDate = {};
  tugasList.forEach((t) => { (tasksByDate[t.deadline] ||= []).push(t); });

  const isoFor = (day) => `${year}-${String(month + 1).padStart(2, "0")}-${String(day).padStart(2, "0")}`;
  const dayTasks = selectedDate ? (tasksByDate[selectedDate] || []) : [];

  return (
    <div className="space-y-3 pb-4">
      <Card>
        <div className="flex items-center justify-between mb-3">
          <button onClick={() => setCalMonth(new Date(year, month - 1, 1))} className="text-sm px-2" style={{ color: accent }}>‹</button>
          <p className="text-sm font-bold" style={{ color: INK, fontFamily: "'Sora',sans-serif" }}>{BULAN[month]} {year}</p>
          <button onClick={() => setCalMonth(new Date(year, month + 1, 1))} className="text-sm px-2" style={{ color: accent }}>›</button>
        </div>
        <div className="grid grid-cols-7 gap-1 text-center mb-1">
          {HARI.map((h) => <span key={h} className="text-[10px] font-semibold" style={{ color: MUTED }}>{h}</span>)}
        </div>
        <div className="grid grid-cols-7 gap-1">
          {cells.map((day, i) => {
            if (!day) return <div key={i} />;
            const iso = isoFor(day);
            const list = tasksByDate[iso] || [];
            const isToday = iso === fmtISO(today);
            const isSel = iso === selectedDate;
            const topColor = list.length ? PRIORITAS[[...list].sort((a, b) => (a.status === "selesai") - (b.status === "selesai"))[0].prioritas] : null;
            return (
              <button key={i} onClick={() => setSelectedDate(iso)} className="aspect-square flex flex-col items-center justify-center rounded-lg text-xs"
                style={{
                  background: isSel ? accent : isToday ? PRIMARY_BG : "transparent",
                  color: isSel ? "#fff" : INK,
                  fontWeight: isToday ? 700 : 500,
                }}>
                {day}
                {list.length > 0 && <span className="w-1 h-1 rounded-full mt-0.5" style={{ background: isSel ? "#fff" : topColor }} />}
              </button>
            );
          })}
        </div>
      </Card>

      <p className="text-xs font-semibold px-1" style={{ color: MUTED }}>
        {selectedDate ? fmtLong(selectedDate) : "Pilih tanggal untuk lihat tugas"}
      </p>
      {selectedDate && dayTasks.length === 0 && (
        <Card className="text-center py-6"><p className="text-sm" style={{ color: MUTED }}>Tidak ada tugas tanggal ini.</p></Card>
      )}
      {dayTasks.map((t) => (
        <button key={t.id} onClick={() => onSelectTugas(t.id)} className="w-full text-left">
          <Card>
            <div className="flex items-start gap-3">
              {mode === "siswa" ? (
                t.status === "selesai" ? <CheckCircle2 size={18} color={TEAL} /> : <Circle size={18} color="#C9C7DE" />
              ) : (
                <Users size={16} color={TEAL} className="mt-0.5" />
              )}
              <div className="flex-1">
                <p className="text-sm font-semibold" style={{ color: INK }}>{t.nama}</p>
                <span className="text-[11px]" style={{ color: PRIORITAS[t.prioritas] }}>{t.mapel} · {t.prioritas}</span>
                {mode === "guru" && (
                  <p className="text-[11px] mt-0.5" style={{ color: MUTED }}>
                    {Object.keys(t.pengumpulan || {}).length}/{AKUN_SISWA.length} mengumpulkan
                  </p>
                )}
              </div>
              <ChevronRight size={14} color="#B4B2C7" className="mt-0.5" />
            </div>
          </Card>
        </button>
      ))}
    </div>
  );
}

/* -------------------------- STATISTIK (siswa) ---------------------------*/
function StatistikScreen({ tugasList, accent = PRIMARY }) {
  const total = tugasList.length;
  const selesai = tugasList.filter((t) => t.status === "selesai").length;
  const pct = total ? Math.round((selesai / total) * 100) : 0;
  const r = 42, c = 2 * Math.PI * r;

  const perMapel = {};
  tugasList.forEach((t) => { perMapel[t.mapel] = (perMapel[t.mapel] || 0) + 1; });
  const perPrioritas = { Tinggi: 0, Sedang: 0, Rendah: 0 };
  tugasList.forEach((t) => { perPrioritas[t.prioritas]++; });

  return (
    <div className="space-y-3 pb-4">
      <Card className="flex flex-col items-center py-6">
        <svg width="110" height="110" viewBox="0 0 100 100">
          <circle cx="50" cy="50" r={r} fill="none" stroke="#EFEEFA" strokeWidth="9" />
          <circle cx="50" cy="50" r={r} fill="none" stroke={accent} strokeWidth="9" strokeLinecap="round"
            strokeDasharray={c} strokeDashoffset={c - (pct / 100) * c} transform="rotate(-90 50 50)" />
          <text x="50" y="55" textAnchor="middle" fontSize="20" fontWeight="700" fill={INK}>{pct}%</text>
        </svg>
        <p className="text-xs mt-2" style={{ color: MUTED }}>{selesai} dari {total} tugas terkumpul</p>
      </Card>

      <div className="grid grid-cols-2 gap-3">
        <Card className="text-center"><p className="text-2xl font-extrabold" style={{ color: accent, fontFamily: "'Sora',sans-serif" }}>{total - selesai}</p><p className="text-[11px]" style={{ color: MUTED }}>Belum Selesai</p></Card>
        <Card className="text-center"><p className="text-2xl font-extrabold" style={{ color: accent, fontFamily: "'Sora',sans-serif" }}>{selesai}</p><p className="text-[11px]" style={{ color: MUTED }}>Selesai</p></Card>
      </div>

      <Card>
        <p className="text-xs font-semibold mb-3" style={{ color: MUTED }}>Berdasarkan prioritas</p>
        {Object.entries(perPrioritas).map(([p, n]) => (
          <div key={p} className="mb-2">
            <div className="flex justify-between text-xs mb-1"><span style={{ color: INK }}>{p}</span><span className="font-semibold" style={{ color: INK }}>{n}</span></div>
            <div className="h-1.5 bg-slate-100 rounded-full overflow-hidden"><div className="h-full rounded-full" style={{ width: total ? `${(n / total) * 100}%` : 0, background: PRIORITAS[p] }} /></div>
          </div>
        ))}
      </Card>

      <Card>
        <p className="text-xs font-semibold mb-3" style={{ color: MUTED }}>Tugas per mata pelajaran</p>
        {Object.entries(perMapel).map(([m, n]) => (
          <div key={m} className="flex justify-between items-center py-1.5 text-sm">
            <span style={{ color: INK }}>{m}</span>
            <span className="px-2 py-0.5 rounded-full text-xs font-semibold" style={{ background: PRIMARY_BG, color: PRIMARY }}>{n}</span>
          </div>
        ))}
        {total === 0 && <p className="text-xs" style={{ color: MUTED }}>Belum ada tugas.</p>}
      </Card>
    </div>
  );
}
