pobranie i zalogowanie sie do cli i utworzenie na dysku pliku z poświadczeniami dla terraform

gcloud auth application-default login


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


aby sprawdzic czy wszystkie api zostaly wlaczone

gcloud services list --enabled


dodanie providera i stworzenie vpc w terraform

po utworzeniu plików main.tf i vpc.tf sprawdzam polaczenie

terraform init
terraform plan

zatwierdzenie zmian i wyslanie zapytania do privdera, czyli utworzenie vpc

terraform apply

sprawdzenie czy siec main-vpc poprawnie sie utworzyla

gcloud compute networks list

utworzenie podsieci o adresie 10.0.1.0 z maską /24 w pliku subnets.tf.
do pliku subnets.tf dodaję również Cloud Router i NAT aby maszyny mogły pobrać np. nginx czy python mimo braku publicznego ip.

maszyna z webserver nie bedzie miala publicznego ip, aby zminimalizować pole do ataków. Komunikacja ze strona www bedzie odbywala sie przez loadbalancer, który już ma publiczne IP i umozliwi dostep do strony, ale zablokuje mozliwosc laczenia sie do maszyny przez ssh. do konfiguracji maszyny uzywany bedzie Identity-Aware Proxy (IAP), czyli tunelowanie SSH przy pomocy serwerów google.

IAP wie ze mam dostep poprzez konto google i uprawnienia IAM. gcloud tworzy pare kluczy public i private, przy próbie połączenia sie przez tunelowane SSH. jesli mam uprawnienia, to IAP łączy sie z maszyna przez port 22 wewnątrz sieci VPC i jest on pośrednikiem.

w regułach firewall dodano SSH port 22 dla zakresu adresów usług google IAP oraz port 80 http dla adresów loadbalancera i health check. 

dodanie pliku iam.tf do utworzenia konta dla VM z web serverem do publikacji pub/sub i dla cloud run function z wysyłaniem email. Ogranicza to dostęp do wszystkich funkcji, oprócz tych dozwolonych, do poprawnego działania projektu.

utworzenie pliku messaging.tf z Topic dla pub/sub oraz secrets.tf który jest pustym uchwytem dla klucza api dla sendgrid, który będzie ukryty w secret manager google.
pub/sub dla formularza na stronie www jest po to, aby po wyslaniu formularza wiadomosc zostala zakolejkowana do przetworzenia przez funkcje.

aby potwierdzic utworzenie topicu dla pubsub

gcloud pubsub topics list

utworzenie VM e2-micro, wystarczajaca na potrzeby projektu, z metadanymi startup, które definują pobranie serwera nginx do zhostowania strony html. 

po utworzeniu maszyny i odpaleniu startup z metadata service, nginx jest zainstalowany ze strona html. Mozna ją podejrzec łącząc się z VM przez tunelowanie ssh przez Identity-Aware Proxy (IAP), musimy to tak zrobić ponieważ web server nie ma publicznego adresu ip, aby ograniczyć pole do ataków.

gcloud compute ssh web-server --zone=europe-west1-b --tunnel-through-iap

mozna podejrzec strone html poprzez

curl http://localhost

teraz dodam aplikacje python, która za pomocą serwera flask nasłuchuje na porcie 5000, jeśli przyjdzie zapytanie typu POST na endpoint /api/submit z formularza, to wyciąga dane z request.form, pakuje do formatu json i przekazuje je do publishera pubsub. aplikacja python ma dostęp do usługi pubsub przez service account dodane do VM.

aby sprawdzic komunikacje frontend backend wysyłam zapytanie do serwera

curl -X POST -d "email=test@email.com&message=test" http://localhost/api/submit

aplikacje python wrzucam do systemd, aby nie była jako zwykły proces, tylko usługa, która jest automatycznie zarządzana przez system i restartowana w razie awarii.