<p align="center">
  <img src="https://engeto.cz/wp-content/uploads/2019/01/engeto-square.png" width="200" height="200">
</p>

# Java - 8. lekce

## Co bychom měli mít hotové

 - Základy OOP
 - Základy Mavenu

## Co nás čeká

- Maven Wrapper
- Testovací pyramida a principy testování
- Unit testování pomocí JUnit 5 

## Maven

V minulé lekci jsem si vysvětlili základy práce s nástrojem Maven. V dnešní lekci se k tomu vrátíme a ukážeme si co to je Maven Wrapper a k čemu to slouží.

### Maven Wrapper

Maven wrapper zabalí závislost na knihovně Maven dovnitř samotného projektu. Stačí jenom jednou spustit inicializaci Maven Wrapperu pomocí spuštění Maven goal: `io.takari:maven:wrapper`. 

a poté můžeme volat místo `mvn clean install` speciální příkaz, který zajistí stažení správné verze Mavenu `mvnw.cmd clean install`

Hlavní výhoda tohoto přístupu je ten, že není nutné nic instalovat ani konfigurovat a tím pádem je mnohem jednodušší zajistit konzistentní build na různých prostředích.

## Testování aplikace

### Testovací pyramida

End to End testy
Integrační testy
Unit testy

## JUnit 5
Nejpoužívanější knihovnou na unit testy v Javě je JUnit. Poslední verze je verze 5, která přináší mnohé vylepšení.

Pro přidání do Maven projektu je nutné přidat následující závislosti:

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.engeto</groupId>
    <artifactId>lekce08_unit_testing</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>10</maven.compiler.source>
        <maven.compiler.target>10</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>

        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>5.7.0</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>5.7.0</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-engine</artifactId>
            <version>5.7.0</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.22.2</version>
            </plugin>
        </plugins>
    </build>

</project>
```

Pokud potřebujeme testy přeskočit, tak můžeme přidat následující parametry 

`mvnw.cmd clean install -DskipTests -Dmaven.test.skip=true`

`-DskipTests` - přeskočí spouštení testů

`-Dmaven.test.skip=true`  - testy se nebudou ani buildovat

### Mockování
Další nástroj, který je užitečný při testování je Mockování. Časté dilemna při unit testování je jak správně oddělit jednotlivé moduly v případě, že mají nějaké závislosti na dalších modulech. Jak izolovat logiku aplikace od volání nějakého API?

Řešení je mockování pomocí knihovny Mockito. Pomocí tohoto nástroje nasimulujeme chování daného objektu, tak aby nám to správně pasovalo do našich testů. Je to mnohem jednodušší než oddělit interface a vytvořit si vlastní mini-implementaci.

```
        <dependency>
            <groupId>org.mockito</groupId>
            <artifactId>mockito-core</artifactId>
            <version>3.6.28</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.mockito</groupId>
            <artifactId>mockito-junit-jupiter</artifactId>
            <version>3.6.28</version>
        </dependency>
```

Základní použití je velice jednoduché, na definovaný objekt dáme anotaci `@Mock` a poté můžeme použit `Mockito.when(objekt.metoda(parametry)).thenReturn(navratovaHodnota);`

```
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.List;

@ExtendWith(MockitoExtension.class)
public class MockitoTest {

    @Mock
    private List list;

    @Mock
    private Kalkulacka kalkulacka;

    @Test
    public void testMock(){

        Mockito.when(list.get(0)).thenReturn(150);

        list.add(100);
        Assertions.assertEquals(150, list.get(0));
    }

    @Test
    public void testMockKalkulacka(){

        Mockito.when(kalkulacka.nasobeni(5.0,5.0)).thenReturn(25.0);

        Assertions.assertEquals(25, kalkulacka.nasobeni(5.0,5.0));
    }
}

```
