# Proiect BAZE DE DATE: Sala de sport
## 1. Descrierea temei 
Proiectul reprezinta baza de bate a unei sali de sport.
Exista mai multe sedii ale salii de sport, fiecare sediu avand denumire, capacitate si data de
deschidere proprie. Fiecare sediu se afla la o adresa care contine oras, strada si numar. Fiecare
sediu are unul sau mai multi angajati.
Pentru fiecare angajat stim numele, prenumele, telefonul, emailul, salariul, bonusul, sediul in
care acesta lucreaza, functia acestuia si managerul care ii este superior.Fiecare angajat poate
(daca acesta este antrenor) sa sustina una sau mai multe ore de antrenament (sedinte) impreuna
cu un client.
Fiecare sedinta are un tip de antrenament (pentru fiecare tip de antrenament se cunosc denumirea
care este alcatuita din initiala sportului practicat si numarul sedintei respectivului client si nivelul
- un numar de la 1 la 5, reprezentand dificultatea.
Fiecare client participa la una sau mai multe sedinte. Despre fiecare client se cunosc urmatoarele:
numele, prenumele, telefonul, data nasterii, emailul, data de incepere si data de expirare a
abonamentului si tipul de abonament. Fiecare client are un abonament (iar un abonament poate fi
avut de unul sau mai multi clienti).
Fiecare abonament are un tip de antrenament aferent un tip de antrenament (fiecare tip de
antrenament face parte dintr-un abonament).

## 2. Schema conceptuala pentru modelarea temei alese. Din schema va trebui sa rezulte tipul
legăturilor dintre entităţi după modelul schemei de la seminar. Se poate realiza folosind
orice instrument sau site (e.g., www.draw.io)
![image](https://github.com/dumitriu-ana/Proiect_Baze_de_Date_Sala/assets/72306782/4081e7c7-d58d-49ab-b9bd-5a6006b2f5a6)

## 3. Crearea tabelelor: CREATE (AD_ADRESE, AD_SEDII, AD_ANGAJATI, AD_TIP_ANTRENAMENTE, AD_ABONAMENTE, AD_CLIENTI, AD_SEDINTE).
CREATE TABLE AD_ADRESE
(
ID_ADRESA NUMBER(3) CONSTRAINT ID_ADRESA PRIMARY KEY,
ORAS VARCHAR2(50),
STRADA VARCHAR(50),
NUMAR VARCHAR2(5)
);

## 4. Exemple cu operații de actualizare a datelor: INSERT, UPDATE, DELETE, MERGE
## --1. INSERAREA DATELOR IN TABELE:
- INSERT INTO AD_ADRESE VALUES(1, 'Bucuresti', 'Poiana Florilor', 12);
- INSERT INTO AD_ANGAJATI VALUES(10, 'Dumitriu', 'Ana Maria', '+40727274214', 'anadumitriu@gmail.com',
4200, 0.4, 10, 'Manager', null);
- INSERT INTO AD_TIP_ANTRENAMENTE VALUES(1, 'Cardio', 3);
- INSERT INTO AD_ABONAMENTE VALUES(21, 'C', 150, 1);
- INSERT INTO AD_CLIENTI VALUES(101, 'Catinca', 'Rebic', '+40733823566', TO_DATE('26-FEB-1997', 'DD-
MON-YYYY'), 'catincarebic@gmail.com', TO_DATE('23-MAR-2020', 'DD-MON-YYYY'), 22, TO_DATE('23-
APR-2020', 'DD-MON-YYYY'));
- INSERT INTO AD_SEDINTE VALUES(70, 'C7', 30, 11, 1, 102);

## -- 2. Interogare 
SELECT ID_ANGAJAT, NUME, PRENUME, TELEFON, EMAIL, SALARIU, BONUS, ID_SEDIU, FUNCTIE,
ID_MANAGER, SALARIU*(1+BONUS) AS VENIT
FROM AD_ANGAJATI
WHERE LOWER(NUME)='dumitriu';

![image](https://github.com/dumitriu-ana/Proiect_Baze_de_Date_Sala/assets/72306782/b2cd8d94-02be-46c2-b1d5-2e23bece7dd9)

## -- 3. Sa se afiseze toti angajatii mai putin cel din functia de manager si cei din functia de contabil
care au bonus intre 0.2 si 0.5
SELECT ID_ANGAJAT, NUME, PRENUME, TELEFON, EMAIL, BONUS, FUNCTIE
FROM AD_ANGAJATI
WHERE FUNCTIE NOT IN('Manager' , 'Contabil') AND (BONUS BETWEEN 0.2 AND 0.5);

![image](https://github.com/dumitriu-ana/Proiect_Baze_de_Date_Sala/assets/72306782/be917cc8-415d-4629-ae70-5fac7eebcda1)

## --4. Sa se afiseze sediile care au mai mult de 2 angajati si numarul de angajati aferent (JOIN)
SELECT S.ID_SEDIU, S.DENUMIRE, S.CAPACITATE, COUNT(*) AS NUMAR_ANGAJATI
FROM AD_ANGAJATI A JOIN AD_SEDII S ON S.ID_SEDIU=A.ID_SEDIU
GROUP BY S.ID_SEDIU, S.DENUMIRE, S.CAPACITATE
HAVING COUNT(*)>=2
ORDER BY 1;

![image](https://github.com/dumitriu-ana/Proiect_Baze_de_Date_Sala/assets/72306782/29a481db-dc39-4d04-81cb-d4106695a60b)

## --5. Sa se afiseze angajatii care au salariul mai mic decat media salariilor tuturor angajatilor
SELECT ID_ANGAJAT, NUME, PRENUME, SALARIU, NVL(BONUS, 0) BONUS, SALARIU+
SALARIU*NVL(BONUS, 0) VENIT
FROM AD_ANGAJATI
WHERE SALARIU+ SALARIU*NVL(BONUS, 0)<(SELECT AVG(SALARIU+ SALARIU*NVL(BONUS, 0))
FROM AD_ANGAJATI)
ORDER BY VENIT DESC;

![image](https://github.com/dumitriu-ana/Proiect_Baze_de_Date_Sala/assets/72306782/a32c7c49-83a5-4fdc-9036-feefe83c9666)

## --6. Sa se afiseze subordonatii directi si indirecti ai angajatului cu id_anhajat egal cu 10
SELECT ID_ANGAJAT, LPAD(' ',LEVEL*4-2)||NUME||' '||PRENUME ANGAJAT,
SALARIU, BONUS, ID_MANAGER, LEVEL FROM AD_ANGAJATI
CONNECT BY PRIOR ID_ANGAJAT=ID_MANAGER
START WITH ID_ANGAJAT=10
ORDER BY LEVEL;

![image](https://github.com/dumitriu-ana/Proiect_Baze_de_Date_Sala/assets/72306782/e296cf6f-1679-41c6-a0a5-7f6dfd131deb)

