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

