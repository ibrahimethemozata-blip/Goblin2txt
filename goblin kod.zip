# goblin_sim_gui.py
# Zararsız simülasyon: "Goblin Type-C Virus" tarama + seçenekler
# Python 3 standard lib (tkinter) ile çalışır. Hiçbir gerçek dosyayı değiştirmez.

import tkinter as tk
from tkinter import ttk, messagebox
import random
import time
import threading

APP_TITLE = "Goblin Type-C — Tarama Simülasyonu (Güvenli)"
SCAN_STEPS = [
    "Dosyalar taranıyor",
    "Kayıt defteri denetleniyor",
    "Çalışan süreçler inceleniyor",
    "Ağ bağlantıları kontrol ediliyor",
    "Şüpheli imzalar aranıyor",
]

class ScanApp:
    def __init__(self, root):
        self.root = root
        root.title(APP_TITLE)
        root.resizable(False, False)
        self.mainframe = ttk.Frame(root, padding=12)
        self.mainframe.grid(row=0, column=0, sticky="nsew")

        # Başlık / bilgi
        ttk.Label(self.mainframe, text="Goblin Type-C Virüs Simülasyonu", font=("Segoe UI", 14, "bold")).grid(row=0, column=0, columnspan=3, pady=(0,8))
        self.status_lbl = ttk.Label(self.mainframe, text="Hazır. Başlatmak için 'Tarama Başlat' düğmesine basın.", wraplength=420)
        self.status_lbl.grid(row=1, column=0, columnspan=3, sticky="w")

        # Progress bar ve adım bilgisi
        self.pb = ttk.Progressbar(self.mainframe, orient="horizontal", length=420, mode="determinate")
        self.pb.grid(row=2, column=0, columnspan=3, pady=8)
        self.step_lbl = ttk.Label(self.mainframe, text="")
        self.step_lbl.grid(row=3, column=0, columnspan=3, sticky="w")

        # Butonlar
        self.start_btn = ttk.Button(self.mainframe, text="Tarama Başlat", command=self.start_scan)
        self.start_btn.grid(row=4, column=0, pady=(10,0), sticky="ew")
        self.stop_btn = ttk.Button(self.mainframe, text="Duraklat", command=self.pause_scan, state="disabled")
        self.stop_btn.grid(row=4, column=1, pady=(10,0), sticky="ew")
        self.reset_btn = ttk.Button(self.mainframe, text="Sıfırla", command=self.reset, state="disabled")
        self.reset_btn.grid(row=4, column=2, pady=(10,0), sticky="ew")

        # Eylem butonları (sonradan etkinleşir)
        self.action_frame = ttk.LabelFrame(self.mainframe, text="Önerilen Eylemler (simülasyon)", padding=8)
        self.action_frame.grid(row=5, column=0, columnspan=3, pady=(12,0), sticky="ew")
        self.action_frame.grid_remove()

        self.quarantine_btn = ttk.Button(self.action_frame, text="Karantina (Simülasyon)", command=lambda: self.perform_action('quarantine'))
        self.clean_btn = ttk.Button(self.action_frame, text="Tam Temizleme (Simülasyon)", command=lambda: self.perform_action('clean'))
        self.ignore_btn = ttk.Button(self.action_frame, text="Yoksay (Simülasyon)", command=lambda: self.perform_action('ignore'))
        self.info_btn = ttk.Button(self.action_frame, text="Daha Fazla Bilgi", command=lambda: self.perform_action('info'))
        self.exit_btn = ttk.Button(self.action_frame, text="Çıkış", command=self.root.quit)

        # action layout
        self.quarantine_btn.grid(row=0, column=0, padx=6, pady=6, sticky="ew")
        self.clean_btn.grid(row=0, column=1, padx=6, pady=6, sticky="ew")
        self.ignore_btn.grid(row=0, column=2, padx=6, pady=6, sticky="ew")
        self.info_btn.grid(row=1, column=0, columnspan=2, padx=6, pady=(0,6), sticky="ew")
        self.exit_btn.grid(row=1, column=2, padx=6, pady=(0,6), sticky="ew")

        # internal state
        self._scanning = False
        self._paused = False
        self._scan_thread = None

    def start_scan(self):
        if self._scanning:
            return
        # reset UI
        self.pb['value'] = 0
        self.step_lbl['text'] = ""
        self.status_lbl['text'] = "Taramaya hazırlanılıyor..."
        self.start_btn['state'] = 'disabled'
        self.stop_btn['state'] = 'normal'
        self.reset_btn['state'] = 'disabled'
        self.action_frame.grid_remove()

        self._scanning = True
        self._paused = False
        self._scan_thread = threading.Thread(target=self._run_scan, daemon=True)
        self._scan_thread.start()

    def pause_scan(self):
        if not self._scanning: return
        self._paused = not self._paused
        self.stop_btn['text'] = "Devam Et" if self._paused else "Duraklat"
        self.status_lbl['text'] = "Tarama duraklatıldı." if self._paused else "Tarama devam ediyor..."

    def reset(self):
        if self._scanning:
            messagebox.showinfo("Bilgi", "Tarama devam ederken sıfırlanamaz.")
            return
        self.pb['value'] = 0
        self.step_lbl['text'] = ""
        self.status_lbl['text'] = "Hazır. Başlatmak için 'Tarama Başlat' düğmesine basın."
        self.start_btn['state'] = 'normal'
        self.stop_btn['state'] = 'disabled'
        self.reset_btn['state'] = 'disabled'
        self.action_frame.grid_remove()

    def _run_scan(self):
        total_steps = len(SCAN_STEPS)
        overall_progress = 0
        # show initial message
        self._ui_update(lambda: self.status_lbl.config(text="Sistem taranıyor... Lütfen bekleyin."))
        # iterate steps
        for idx, step in enumerate(SCAN_STEPS, start=1):
            # wait while paused
            while self._paused:
                time.sleep(0.1)
                if not self._scanning:
                    return
            self._ui_update(lambda s=step: self.step_lbl.config(text=f"Adım: {s}"))
            # simulate step progress (0..100)
            for p in range(0, 101, random.randint(6,12)):
                if not self._scanning:
                    return
                while self._paused:
                    time.sleep(0.1)
                # update per-step and overall bar
                overall = ((idx - 1) + (p/100)) / total_steps * 100
                self._ui_update(lambda val=overall: self.pb.config(value=val))
                time.sleep(0.03 + random.random()*0.06)
            overall_progress = idx / total_steps * 100
            self._ui_update(lambda val=overall_progress: self.pb.config(value=val))
            # small pause between steps
            time.sleep(0.2 + random.random()*0.5)

        # determine if threat found (simülasyon, %85 ihtimal)
        found = random.random() < 0.85
        if found:
            self._on_found()
        else:
            self._ui_update(lambda: self.status_lbl.config(text="TARAMA SONUCU: Hiçbir tehdit bulunamadı. Sistem temiz görünüyor."))
            self._ui_update(lambda: self.reset_btn.config(state='normal'))
            self._ui_update(lambda: self.stop_btn.config(state='disabled'))
            self._scanning = False
            self._ui_update(lambda: self.start_btn.config(state='normal'))

    def _on_found(self):
        # update UI -> show details and actions
        def show_found():
            self.status_lbl.config(text="TARAMA SONUCU: 'Goblin Type-C Virus' tespit edildi!")
            self.step_lbl.config(text="Tehdit: Goblin Type-C (Yüksek risk, simülasyon)")
            # show a short info message box
            messagebox.showwarning("Tehdit Bulundu", "Goblin Type-C Virus tespit edildi (simülasyon). Önerilen eylemi seçin.")
            self.action_frame.grid()  # göster
            self.reset_btn.config(state='normal')
            self.stop_btn.config(state='disabled')
            self.start_btn.config(state='disabled')
        self._ui_update(show_found)
        self._scanning = False

    def perform_action(self, action):
        # hepsi simülasyon: sadece görsel geri bildirim verir
        if action == 'quarantine':
            self.status_lbl.config(text="Karantina uygulanıyor (simülasyon)...")
            self._simulate_action_steps(["Dosya izole ediliyor", "İşlem izleri güncelleniyor"], final_text="Karantina tamamlandı. (Simülasyon)")
        elif action == 'clean':
            self.status_lbl.config(text="Tam temizleme başlatıldı (simülasyon)...")
            self._simulate_action_steps(["Zararlı izler temizleniyor", "Sistem dosyaları doğrulanıyor", "Geri yükleme oluşturuluyor"], final_text="Temizleme başarılı. (Simülasyon)")
        elif action == 'ignore':
            self.status_lbl.config(text="Tehdit yoksayıldı. (Simülasyon) Sistem izlemede kalacak.")
            messagebox.showinfo("Yoksay", "Tehdit yoksayıldı (simülasyon). Gerçek bir durumda dikkatli olun.")
        elif action == 'info':
            info = (
                "Ad: Goblin Type-C (Simülasyon)\n"
                "Tehdit Seviyesi: Yüksek (simülasyon)\n"
                "Bulaşma: E-posta eki / kötü amaçlı betik (simülasyon)\n"
                "Not: Bu sadece gösterim amaçlıdır. Gerçek şüpheler için güvenilir antivirüs kullanın."
            )
            messagebox.showinfo("Daha Fazla Bilgi", info)

    def _simulate_action_steps(self, steps, final_text="İşlem tamamlandı (simülasyon)"):
        # küçük animasyon için ayrı thread
        def worker():
            for s in steps:
                self._ui_update(lambda t=s: self.step_lbl.config(text=f"> {t}"))
                # animate small progress inside pb (local)
                for i in range(0, 101, 8):
                    self._ui_update(lambda v=i: self.pb.config(value=v))
                    time.sleep(0.05 + random.random()*0.06)
            self._ui_update(lambda: self.step_lbl.config(text=""))
            self._ui_update(lambda: self.status_lbl.config(text=final_text))
            messagebox.showinfo("Simülasyon", final_text)
        threading.Thread(target=worker, daemon=True).start()

    def _ui_update(self, func):
        # schedule UI update safely from threads
        self.root.after(1, func)

def main():
    root = tk.Tk()
    style = ttk.Style(root)
    # default theme, but set some widget options if available
    try:
        style.theme_use('clam')
    except:
        pass
    app = ScanApp(root)
    root.mainloop()

if __name__ == "__main__":
    main()
