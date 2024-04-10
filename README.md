# Serializarea Obiectelor

- Toate clasele ce vor fi serializate trb sa implementeze Serializable (implements Serializable), cuv transient impiedica serializarea (private transient id - nu va serializa id-ul)

- Functiile pt serializare si deserializare:

```java
static void scrie(Object o, String fis) {
    try {
        FileOutputStream f = new FileOutputStream(fis);
        ObjectOutputStream oos = new ObjectOutputStream(f);
        oos.writeObject(o);
        oos.close();
        f.close();
    }
    catch (IOException e) {
        e.printStackTrace();
    }
}

static Object citeste(String fis) {
    try {
        FileInputStream f = new FileInputStream(fis);
        ObjectInputStream ois = new ObjectInputStream(f);
        Object o=ois.readObject();
        ois.close();
        f.close();
        return o;
    }
    catch (IOException | ClassNotFoundException e) {
        e.printStackTrace();
    }
    return null;
}

```

# Documentatia si generarea ei

``` java

/**
 * 
 */

```

- Se foloseste comentariul de documentatie de mai sus pus inaintea metodei, clasei, constructorului etc. pentru a genera documentatia

# Gasirea cwd in java

- Va returna directorul de lucru curent


```java
    System.getProperty("user.dir")
```

# String cleanup

```java
    String data = line.replaceAll(",","");
    String[] arrOfStrings = data.split(" ");
```

- `line.replaceAll` - inlocuieste toate caracterele specificate cu un alt caracter

- `data.split` - imparte datele intr-un vector dupa un caracter

# Proiecte maven si Pom.xml

- In Pom.xml putem sa inseram package-uri pe care IDE-ul le va instala automat in proiect

``` java

    <dependencies>
        <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        <version>2.14.2</version>
        </dependency> 
    </dependencies>

```

- Toate dependintele trb adaugate in tagul dependencies

- Citirea si scrierea din fisier JSON:

```java
    public static void scriere(List<Persoana> lista) {
        try {
        ObjectMapper mapper=new ObjectMapper();
        File file=new File("src/main/resources/persoane.json");
        mapper.writeValue(file,lista);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static List<Persoana> citire() {
        try {
        File file=new File("src/main/resources/persoane.json");
        ObjectMapper mapper=new ObjectMapper();
        List<Persoana> persoane = mapper
        .readValue(file, new TypeReference<List<Persoana>>(){});
        return persoane;
        } catch (IOException e) {
        e.printStackTrace();
        }
    return null;
    } 
```

- Inlocuirea Obiectului Persoana cu orice alt tip de obiect este nevoie

-Junit si testele

``` java

<dependency>
<groupId>org.junit.jupiter</groupId>
<artifactId>junit-jupiter-api</artifactId>
<version>5.9.2</version>
<scope>test</scope>
</dependency>

@Test
void test1_suma() {
    Calculator c=new Calculator(4,5);
    assertEquals(9, c.suma());
 }
@Test
void test2_suma() {
    Calculator c=new Calculator(2,2);
    assertTrue(c.suma()==4);
 } 

@Test
void test3_suma() {
    Calculator c=new Calculator(3,7);
    assertFalse(c.suma()!=10);
 } 

```

# Jackson Handling Date and Time

```java
ObjectMapper mapper=new ObjectMapper();
mapper.registerModule(new JavaTimeModule()); 
```
- Adaugarea codului pt maparea var de tip DateTime

-Scrierea datelor ca Stringuri:

```java
mapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS); 
```

# Colectii Polimorfe

- Daca avem un set de obiecte si dorim sa facem legatura intre clasa de baza si clasele derivate avem nevoie de urmatoarele intructiuni:

```java
mapper.activateDefaultTyping(mapper.getPolymorphicTypeValidator()); 

si

@JsonTypeInfo(use = JsonTypeInfo.Id.CLASS) 
```

`mapper.activateDefaultTyping(mapper.getPolymorphicTypeValidator());` - este folosit in functia de citire/scriere

`@JsonTypeInfo(use = JsonTypeInfo.Id.CLASS)` - este folosit deasupra clasei de baza pentru ca ide-ul sa isi dea seama cum va realiza polimorfismul

# Streamuri

- Un stream este un iterator al carui rol este de a accepta un set de actiuni sa fie aplicate fiecaruia din
elementele pe care le contine

``` java

class MainApp {
    public static void main(String[] args) {
        //metoda List.of() introdusa in Java 9, creeaza o colectie imutabila de obiecte
        List<String> orase = List.of("Timisoara","Arad","Cluj","Targu-Mures");
        orase.forEach(System.out::println);
        //orase.forEach((o)->System.out.println(o));
        System.out.println("Orase care incep cu litera 'T':");
        orase
        .stream()
        .filter((s) -> s.startsWith("T"))
        .forEach(System.out::println);
        System.out.println("\nLista ordonata a oraselor, scrisa cu litere mari: ");
        orase
        .stream()
        .map(String::toUpperCase)
        //.map((o)->o.toUpperCase())
        .sorted((a, b) -> a.compareTo(b))
        .forEach(System.out::println);
    }
}

```

- Atentie la HashMaps sau containere de genul pentru ca dupa stream trebuie apelata metoda values.

- Ex:

    ``` java
        sout(HashMap) => 
        {1=Carte[titlul=Fram, autorul=Marcel, anul=2001], 2=Carte[titlul=De veghe in lanul de secara, autorul=J.D. SALINGER, anul=1951], 3=Carte[titlul=Mitul lui Camus, autorul=Albert Camus, anul=1942], 4=Carte[titlul=Scurta istorie a omenirii, autorul=Yuval Noah Harari, anul=2011], 5=Carte[titlul=Homo deus - Scurta istorie a viitorului, autorul=Yuval Noah Harari, anul=2015], 6=Carte[titlul=21 de lectii pentru secolul XXI, autorul=Yuval Noah Harari, anul=2018]}
    ```

- Pentru accesarea valorilor trebuie sa facem cv de genul:

    ``` java

        hashCarti.forEach((key,value) -> System.out.println(key + " " + value));

        sau

        hashCarti.values().forEach(System.out::println);

    ```

# Clasa Optional

- Optional este un container care poate sa contina o valoare sau nu. Daca valoarea este prezenta funcaia get() o va returna.

``` java

    System.out.println("7. Datele celei mai vechi carti");
        Optional<Carte> ceaMaiVecheCarte = cartiAutor.stream()
                .min(Comparator.comparingInt(Carte::anul));

        ceaMaiVecheCarte.ifPresent(carte -> {
            System.out.println("Cea mai veche carte:");
            System.out.println(carte.titlul());
            System.out.println(carte.autorul());
            System.out.println(carte.anul());
        });

```