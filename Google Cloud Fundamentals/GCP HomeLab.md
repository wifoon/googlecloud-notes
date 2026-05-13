pobranie i zalogowanie sie do cli i utworzenie na dysku pliku z poświadczeniami dla terraform

gcloud auth login


utworzenie projektu

gcloud projects create ikazior-gcp-homelab --name="GCP Portfolio"

wybranie projektu

gcloud config set project ikazior-gcp-homelab

dodanie budżetu na projekt z alertami e-mail, aby nie wygenerować przypadkowych kosztów.

sprawdzenie czy wszystko się udało

gcloud config list

zainstalowanie terraform i używanie go w projekcie zamiast klikania w konsoli IaC (infrastructure as code)

do tego projektu wykorzystywać będę compute engine, pub/sub, cloud run functions, iam wiec potrzebuje włączenia odpowiednich api

gcloud services enable compute.googleapis.com // do postawienia VM
gcloud services enable pubsub.googleapis.com  // wiadomosci pub/sub
gcloud services enable cloudfunctions.googleapis.com 
gcloud services enable run.googleapis.com  // dla funkcji i kodu serverless
gcloud services enable secretmanager.googleapis.com // schowek na klucze API
gcloud services enable cloudbuild.googleapis.com // silnik do budowania infrastruktury z kodu
gcloud services enable iam.googleapis.com // zarzadzanie tozsamosciami i uprawnieniami