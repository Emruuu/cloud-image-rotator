# cloud-image-rotator

System oparty na Google Cloud, który wykrywa przesłane obrazy do Cloud Storage, obraca je zgodnie z metadanymi i zapisuje wynik w zadanym katalogu. Wszystko zautomatyzowane przez Pub/Sub, Cloud Run i Ansible. Zero ręcznego dłubania.

---

## 📦 Wymagania

- Google Cloud SDK (`gcloud`)
- SSH key (`~/.ssh/id_rsa` + `.pub`)
- Włączone API: Compute Engine, Cloud Run, Cloud Pub/Sub, Cloud Storage
- Python 3.x

## 🧠 Jak to działa?

1. **Użytkownik** wrzuca obrazek do bucketa `gs://bucket-cloud-zaliczenie`
2. **Pub/Sub** wysyła powiadomienie o zdarzeniu
3. **Cloud Function / polling script** odbiera event i pobiera metadane
4. **Obrazek** przesyłany do aplikacji na **Cloud Run**, która go obraca
5. Wynik zapisywany lokalnie na maszynie wirtualnej

---

## 🚀 Uruchomienie

### 1. Zaloguj się do GCP
```bash
gcloud auth login
```
2. Zainstaluj infrastrukturę
```bash
./setup_infrastructure.sh YOUR_PROJECT_ID
```
Skrypt:

tworzy VM

generuje bucket i powiadomienia

buduje i wdraża aplikację na Cloud Run

uruchamia Ansible dla konfiguracji VM

3. Wyślij obrazek do bucketa
```bash
gcloud storage cp books.png gs://bucket-cloud-zaliczenie/books.png \
  --custom-metadata=rotation-angle=172,output-path=/home/user/incoming/
```
Jeśli metadane nie są podane:

rotation-angle domyślnie 90

output-path domyślnie katalog domowy użytkownika na VM

🐍 Komponenty projektu
notification_polling.py
Subskrybuje Pub/Sub

Pobiera obrazek z Cloud Storage

Wyciąga metadane

Przesyła do Cloud Run

Zapisuje wynik na dysku

imgops.py (Cloud Run)
REST API (Flask + Pillow)

Obraca obraz o zadany kąt i zwraca go

setup_infrastructure.sh
Automatyzuje wszystko: VM, bucket, subskrypcje, role IAM, deploy na Cloud Run, Ansible

setup_vm.yml (Ansible)
Konfiguruje środowisko na nowej VM
