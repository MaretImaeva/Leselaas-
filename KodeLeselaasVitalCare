long tidIgjen = 0;
long valgtTid = 0;  //60, 45, 30 eller 15 min
int tidIgjenMin = 0; //dette er ikke helt nødvendig, men det hjelper meg å holde oversikt underveis i koden, fordi heltall er lettere enn long
int valgtTidMin = 0;
int antallLeds = 10;


//-------------------------------------- komponenter og pins:
 
const int led3 = 3; //kobler til pin
const int led4 = 4;
const int led5 = 5;
const int led6 = 6;
const int led7 = 7;
const int led8 = 8;
const int led9 = 9;
const int led10 = 10;
const int led11 = 11;
const int led12 = 12;

int sisteLed = -1; //brukes ved iterasjoner

int antLys = 10;
 
const int ledPins[] = {
  led3, led4, led5, led6, led7, led8, led9, led10, led11, led12
}; //går inn i digital pins fra 3 til og med 12
 
const int hoytaler = 13; //koblet til pin 13
 
const int sensor = 2; //sensor er koblet til pin 2
unsigned long lastMotionTime = 0; //tar tiden i ms 
const unsigned long timeout = 15 * 60 * 1000; // 15 minutter i millisekunder
 
boolean laas = false; //statusen til boksens lås. bestemmer om den da blir åpnet eller lukket
 
//-------------------------------------- knapper:
const int button15 = A0;
const int button30 = A1;
const int button45 = A2;
const int button60 = A3;
const int buttonPause = A4;
const int buttonPluss = A5;
 
 
bool erFerdig= false;

//---------------------------------- noter:

int C4  = 261; //noter sine frekvenser, slik at vi kan lage egne melodier med piezo/høytaler
int Cs4 = 277;
int D4  = 293;
int Ds4 = 311;
int E4  = 330;
int F4  = 349;
int Fs4 = 370;
int G4  = 392;
int Gs4 = 415;
int nA4 = 440;
int As4 = 466;
int B4  = 493;
int C5  = 523;
 

//-----------------------------------------------------
 
 
void setup() {
 
  Serial.begin(9600);
 
  for (int i = 0; i < antallLeds; i++) {
  pinMode(ledPins[i], OUTPUT); //angir alle LEDs som output
  }
 
  pinMode(hoytaler, OUTPUT); //angir høytaler som output
 
  pinMode(button15, INPUT_PULLUP); //angir pinsene som input. HIGH som standard, men det blir LOW ved trykk
  pinMode(button30, INPUT_PULLUP);
  pinMode(button45, INPUT_PULLUP);
  pinMode(button60, INPUT_PULLUP);
  pinMode(buttonPause, INPUT_PULLUP);
  pinMode(buttonPluss, INPUT_PULLUP);
 
}

//-------

 
void loop() {
if (!digitalRead(A0)) { //når knappen trykkes ned, kalles metoden
  knappTrykket(15);
}
else if (!digitalRead(A1)){
  knappTrykket(30);
}
else if (!digitalRead(A2)){
  Serial.println("Pause aktivert");
  pause();
}
else if (!digitalRead(A3)){
  Serial.println("5 min lagt til");
  leggTilTid(5);
}
else if (!digitalRead(A4)) {
  knappTrykket(45);
}
 
if (tidIgjen > 0 && millis() - lastMinutt >= 60000) { //kjører hvert minutt, mens tidIgjen er større enn null
    tidIgjen -= 60000; //oppdaterer long variablen, som håndterer ms
    lastMinutt = millis();
    Serial.print("Tid igjen: ");
    Serial.println((tidIgjen / 60000.0, 2));
    tidIgjenMin--; //for hver iterasjon, teller ned 1 minutt 
}

if (tidIgjenMin <= 0 && valgtTidMin > 0) {
      Serial.println("Registrert ferdig fra loop");
      ferdig();
}

  
  lysLeds(); //oppdaterer og sjekker progresjonen fortløpende, for å aktivere riktige LEDs

 
  // Sjekker og teller sist bevegelse:
  int bevegelse = digitalRead(sensor);
  sjekkBevegelse(); //sjekker bevegelsen for hver iteasjon i loop (konstant sjekking av bevegelse)

  delay(10); //venter 10ms til neste loop
}
 
//-----------------------------------------
 
void knappTrykket(int min){ //registrerer trykkene
  Serial.print(min); //printer til serial monitor 
  Serial.println(" minutter valgt");
    tidIgjen = min*60*1000; 
    valgtTid = min*60*1000;
    laas = true; //intervall startet, låsen skal lukkes
    lyd(); //lyd feedback
    lysStart(); //kaller på metode som gir feedback med trykk 
    valgtTidMin = min; //oppdaterer variablene
    tidIgjenMin = min;
    delay(200); //venter 200ms for å ikke registrere samme knappen ved uhell
 
}
 
 
 
void leggTilTid(int inputMin) { //metode legger til 5 minutter i instansvariablen(e)
  lyd(); //trykk er registrert 
  long minutter = (long)inputMin * 60 * 1000;  //minutter i ms
  tidIgjen += minutter; //oppdaterer antall minutter som gjenstår i ms
  tidIgjenMin += 5; //oppdaterer antall minutter som gjenstår i minutter.
 
 
 
void pause() {
  lyd(); //gir feedback på at pause er registrert
  Serial.println("Pauser i 5 minutter...");
  delay(300000);  // 5 min pause
  lyd(); //sier ifra om at pause er ferdig. Forekommer 3 ganger pga dette
  lyd();
  lyd();
  Serial.println("Pause ferdig");
}
 
 
 
 
void ferdig(){
  if (erFerdig) return; // ikke kjør på nytt
 
  erFerdig = true;
 
  skruAv(); //skrur alle leds av så det kan markeres at tiden er over
 
  for (int i = 0; i < antallLeds; i++) digitalWrite(ledPins[i], HIGH);
  delay(4800);
  skruAv();

  victoryFanfare(); //kaller på ferdig melodi
  laas = false; //låsen åpnes
  valgtTid = tidIgjen = valgtTidMin = tidIgjenMin = 0; //nullstiller alle variabler fordi intervallet er ferdig, og da vil det ikke mikses med neste intervall
}

 
 
void lysLeds() {
  if (valgtTid == 0) return;

  int lysSomSkalLyse = (valgtTidMin-tidIgjenMin)*antallLys/valgtTidMin; 
  //lager en formel for å belyse en andel av lysene, basert på hvor langt tiden har gått

  for (int i = 0; i < antallLeds; i++) {
    if (i < lysSomSkalLyse) { //dersom indeksen til Leden er mindre enn antallet som skal lyse, vil den lyse
      digitalWrite(ledPins[i], HIGH);
    } else {
      digitalWrite(ledPins[i], LOW);
    }
  }
}
 

void skruAV(){ //metode for å skru av alle led lysene
  for (int i = antallLeds; i > 0; i--){
    digitalWrite(ledPins[i], LOW);
    delay(500);
  }
}
 
 
void lysStart() { //metode for å gi visuell feedback til brukere
 
  for (int i = 0; i < antallLeds; i++){
    digitalWrite(ledPins[i], HIGH); //skrur seg på fra bunnen av
    delay(500); //lite delay slik at de ikke skrur på samtidig, men heller "flyter" oppover
  }

  for (int i = 13; i > antallLeds; i--){
    digitalWrite(ledPins[i], LOW); //samme som over, men nå skrus de av fra toppen av
    delay(500);
  }
}
 
 
 
void victoryFanfare() { //ferdig melodi
  int melody[] = {nA4, nA4, nA4, nA4, F4, G4, nA4, G4, nA4}; //lagrer notene i en liste, i riktig rekkefølge (som de forekommer)
  int noteDurations[] = {200, 200, 200, 700, 700, 700, 400, 200, 1500}; //her er antall ms notene varer
 
  for (int i = 0; i < 9; i++) { //itererer (antall noter) ganger
    tone(hoytaler, melody[i], noteDurations[i]); //høytaler spiller note, med tilsvarende varighet
    delay(noteDurations[i] + 20); //lite mellomrom mellom hver
  }
  noTone(hoytaler); //forsikrer oss om at lyd outputen er null
  Serial.println("Burde ha spilt av musikk");
}

 
void lyd() { //enkel lyd for feedback
  tone(hoytaler, nA4, 200);
  delay(200);
  noTone(hoytaler);
  Serial.println("enkel lyd ferdig");
}
 
 
void failedLyd() { //lyd for feilet intervall (bruker var borte/inaktiv for lenge)
  int melody[] = {G4, G4, G4, Ds4}; //samme logikk som victory fanfare
  int noteDurations[] = {200, 200, 200, 600};
 
  for (int i = 0; i < 4; i++) {
    tone(hoytaler, melody[i], noteDurations[i]);
    delay(noteDurations[i] + 20);
  }
  noTone(hoytaler);
  Serial.println("dun dun dun duuuun");
}
 
 
void failedLys(){
    int fadeLeds[] = {3, 5, 6, 9, 10, 11}; //alle pinene med ~, disse klarer å fade med lysene. Derfor benyttes de. Å ikke bruke alle lysene, er også hensiktmessig for å gjøre det lett for bruker å skille feedbacken
 
  for (int r = 0; r < 3; r++) { // blink 3 ganger
    for (int brightness = 0; brightness <= 255; brightness += 5) { //for hver iterasjon økes brightness
      for (int i = 0; i < 5; i++) {
        analogWrite(fadeLeds[i], brightness);
      }
      delay(10);
    }
    for (int brightness = 255; brightness >= 0; brightness -= 5) { //for hver senkes brightness
      for (int i = 0; i < 5; i++) {
        analogWrite(fadeLeds[i], brightness);
      }
      delay(100);
    }
    Serial.println("Skal blinke på led 5,6, 9, 10 og 11");
  }
  for (int i = 0; i < 5; i++) {
    analogWrite(fadeLeds[i], 0); //forsikre om at alle slås av
  }
}
 
 
void sjekkBevegelse(){
  int bevegelse = digitalRead(sensor);  // leser sensoren
 
  if (bevegelse == HIGH) {
    Serial.println("Bevegelse oppdaget!");
    lastMotionTime = millis();  // oppdater siste tid bevegelse
  } else {
    // sjekk om det har gått 15 minutter uten bevegelse
    if (millis() - lastMotionTime >= timeout) { //timeout er definert over setup i koden (verdi av 15 minutter i ms)
      Serial.println("Ingen bevegelse på 15 minutter.");
      tidIgjen = valgtTid;   // tilbakestill tiden
      failedLyd();
      failedLys();
      lastMotionTime = millis();  // nullstill igjen etter reaksjon
    }
  }
 
  delay(1000);  // sjekk hvert sekund
}
 
 
