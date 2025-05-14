# Studieguide - Moment 1: Lösningar, språk & webbserver-grund

## Lärandemål
När du har arbetat med denna guide ska du kunna:
1. Identifiera och beskriva minst tre vanliga server-side-lösningar (språk/ramverk).
2. Förklara hur en HTTP-request processas steg för steg, med kodexempel.
3. Installera och köra XAMPP (Apache + PHP) på Windows, samt testa egna PHP-skript.
4. Skilja mellan statiskt och dynamiskt innehåll, inklusive för- och nackdelar.

---

## 1. Vanliga språk och lösningar för dynamiska webbplatser

**Hur fungerar det?** Server-side-språk bearbetar kod på servern och skickar färdigt HTML/JSON till klienten.

| Lösning              | Kort beskrivning                          | Styrka                                 |
| -------------------- | ----------------------------------------- | -------------------------------------- |
| **PHP**              | Inbäddas i HTML genom `<?php ... ?>`      | Stort ekosystem (WordPress, Laravel)   |
| **Node.js/Express**  | JavaScript på serversidan, asynkront      | Hög prestanda för många förfrågningar  |
| **Python/Flask**     | Mikro-ramverk, enkel uppstart             | Lätt att anpassa                       |
| **Python/Django**    | Komplett ramverk med ORM, admin, templating | Snabb utveckling, "batteries included" |
| **Ruby on Rails**    | Convention over Configuration             | Snabb prototypframtagning              |

**Övning:** Välj två lösningar från tabellen och beskriv i egna ord hur de hanterar en inloggningsförfrågan.

---

## 2. Webbserverns grundfunktionalitet (med exempelkod)

1. **Request**  
   **Exempel HTTP-förfrågan:**
   ```http
   GET /index.php?user=Anna HTTP/1.1
   Host: localhost
   User-Agent: Mozilla/5.0
   ```
   **PHP-kod för att läsa request:**
   ```php
   <?php
   $method = $_SERVER['REQUEST_METHOD'];
   $uri    = $_SERVER['REQUEST_URI'];
   parse_str($_SERVER['QUERY_STRING'], $query);
   echo "Method: $method, URI: $uri<br>";
   echo "Query parameters: <pre>" . print_r($query, true) . "</pre>";
   ?>
   ```

2. **Routing**  
   **Apache .htaccess för enkel routing:**
   ```apacheconf
   RewriteEngine On
   RewriteRule ^about/?$ index.php?page=about [L,QSA]
   ```
   **PHP-kod för routing:**
   ```php
   <?php
   $page = $_GET['page'] ?? 'home';
   if ($page === 'about') {
       include 'about.php';
   } else {
       include 'home.php';
   }
   ?>
   ```

3. **Bearbetning**  
   **Formulärhantering i PHP:**
   ```html
   <!-- form.html -->
   <form action="submit.php" method="POST">
     <label>Meddelande: <input name="message"></label>
     <button type="submit">Skicka</button>
   </form>
   ```
   ```php
   <?php
   // submit.php
   $msg = htmlspecialchars($_POST['message'], ENT_QUOTES, 'UTF-8');
   echo "Du skickade: $msg";
   ?>
   ```

4. **Response**  
   **Skicka JSON-svar:**
   ```php
   <?php
   $data = ['status' => 'ok', 'time' => date('H:i:s')];
   header('Content-Type: application/json');
   echo json_encode($data);
   ?>
   ```

5. **Session**  
   **PHP-sessionhantering:**
   ```php
   <?php
   session_start();
   if (!isset($_SESSION['visits'])) {
       $_SESSION['visits'] = 0;
   }
   $_SESSION['visits']++;
   echo "Du har besökt denna sida {$_SESSION['visits']} gånger.";
   ?>
   ```

> **Reflektionsfråga:** Varför ändrar vi inte databasdata via GET-metoder?

---

## 3. Installation och hantering med XAMPP på Windows

1. **Ladda ner XAMPP**  
   - Gå till https://www.apachefriends.org/download.html.  
   - Välj Windows-version och ladda ner `xampp-windows-x64-<version>.exe`.

2. **Installera XAMPP**  
   - Kör nedladdad `.exe`-fil som administratör.  
   - Acceptera licens och välj komponenter: minst **Apache** och **PHP**.  
   - Standardinstallationsmapp: `C:\xampp`.

3. **Starta Apache och MySQL**  
   - Öppna **XAMPP Control Panel** via Start-menyn.  
   - Klicka **Start** för **Apache**.  
   - Grön statusrad visar att servern körs.

4. **Konfigurera PHP**  
   - I Control Panel, klicka **Config** bredvid **Apache** och välj **php.ini**.  
   - Sök efter `date.timezone` och ändra till:
   ```ini
   date.timezone = "Europe/Stockholm"
   ```
   - Spara filen och klicka **Stop** + **Start** på Apache för att ladda om.

5. **Testa PHP-installationen**  
   - Skapa fil `C:\xampp\htdocs\index.php` med:
   ```php
   <?php
   phpinfo();
   ?>
   ```  
   - Öppna webbläsare och gå till **http://localhost/index.php**.  
   - Du ser PHP-konfigurationssidan.

6. **Köra egna skript**  
   - Skapa `C:\xampp\htdocs\hello.php`:
   ```php
   <?php
   echo "Hello från XAMPP! Server-tid: " . date('Y-m-d H:i:s');
   ?>
   ```  
   - Besök **http://localhost/hello.php** och verifiera utdata.

> **Uppgift:** Dokumentera varje steg med skärmdumpar och testresultat.

---

## 4. Statiskt vs Dynamiskt innehåll (fördjupning)

### Statisk webbplats
- Innehåll levereras som färdiga filer (HTML/CSS/JS).  
- **Fördelar:** Mycket snabb leverans, enkelt att cachea (CDN), låg komplexitet.  
- **Nackdelar:** Begränsad interaktivitet, manuella uppdateringar behövs.

### Dynamisk webbplats
- Innehåll genereras vid förfrågan via server-side-skript (PHP).  
- **Fördelar:** Anpassat innehåll, databasintegration, användarhantering.  
- **Nackdelar:** Högre serverbelastning, kräver caching-lager (t.ex. Redis, Varnish).

### Exempel-scenarier
- **Portfolio (statisk):** Byggd med HTML/CSS, uppdateras genom filändringar.  
- **Blogg (dynamisk):** WordPress med admin-gränssnitt, kommentarer och inloggning.
- **E-handel (hybrid):** Produktlistor renders statiskt, kundvagn och betalning dynamiskt.

### Prestanda & Säkerhet
- **Caching:** Browser cache, server-side cache (OPcache, HTTP-cache).  
- **Säkerhet:** Input-validering, CSRF-skydd, HTTPS (SSL/TLS).

> **Diskussion:** Hur kan du kombinera statisk och dynamisk hosting för bästa balans mellan prestanda och flexibilitet?

---

## Nyckelbegrepp att kunna

- HTTP-metoder (GET, POST)  
- Statuskoder (200, 404, 500)  
- Cookie vs Session  
- DNS, domän och IP  
- SSL/TLS och certifikat

---

### Självtest

1. Beskriv hur en HTTP-request hanteras från klient till PHP-skript på XAMPP.  
2. Ge kodexempel på hur du startar en session i PHP.  
3. Lista stegen för att installera XAMPP och ändra `date.timezone`.  
4. Förklara skillnaden mellan statiska filer och dynamiskt genererade sidor.  
5. Diskutera hur caching kan förbättra prestandan på en dynamisk webbplats.

*Lycka till!*