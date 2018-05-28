# Labor KW15 10.4.2018  

## Theorie von Java  
Am Beginn der Laboreinheit wiederholten wir einige Grundbegriffe der Java Programmierung.  

**Vererbungen**  
In Java gibt es nur Einfachvererbungen. Eine Kindklasse kann nur eine Elternklasse haben aber eine Elternklasse kann mehrere Kindklassen haben.  

**Klassen**  
Eine Klasse ist einfach erklärt nur ein "Bauplan" für ein Java Program. Eine Klasse besteht daher aus Methoden und Atributen.  
Klassen:  
* Interface: Sammlung von Methoden  
* Innere Klasse: In einer innernen Klasse können auch die Atribute der äußeren Klasse übernommen werden.  
* Abstrakte Klasse: Diese Klasse ist noch nicht fertig programmiert. Sie wird aber dazu verwendet um Klassen mit derselben Art zu erstellen. Dabei müssen danach nur einige Funktionen ausimplementiert werden.  
* Anonyme Klasse: Sie ist die einfachste Klasse in Java und hat keinen eigenen Klassennamen.  
 
 **serielle Schnittstelle in Java**  
Damit man in Java mit einem Programm mit der Hardware kommunizieren kann benötigt man die **JVM (Java Virtuelle Maschine)**. Dabei greift das Program auf die virtuelle Maschine zu. Die virtuelle Maschine greift danach auf das Betriebsystem zu und diese danach an die Hardware. Da aber die JVM keine seriellen Schnitstellen unterstützen benötigt Java  zum Beispiel **JNI(Java Native Interface)**.  Wir verwendeten **JJSC (Java Simple Seriel Connector)** in der Schule. Der Vorteil dabei ist das die Bibliotheken sofort automatisch richtig verpackt werden und man diese sofort verwenden kann. Eine weitere Möglichkeit die man installieren kann und auch früher verwendet wurde ist **Java.comm**.    
#### Java Native Interface (JNI)
> JNI ermöglicht es, native Methoden zu schreiben für Situationen, in denen es nicht möglich ist, ausschließlich Java als Programmiersprache einzusetzen. Dies ist der Fall, wenn beispielsweise die Standard-Java-Klassenbibliothek bestimmte Plattform-abhängige Features oder andere Programmbibliotheken nicht unterstützt. Weiterhin ist es per JNI möglich, eine weitere in einer anderen Programmiersprache programmierte Anwendung für Java zugreifbar zu machen. Viele Klassen der Java-Standardbibliothek basieren auf JNI, um beispielsweise die Datei-Ein- und Ausgabe oder Soundwiedergabe zu ermöglichen. Indem Java Leistungs- und Plattform-abhängige Implementierungen in die Standardbibliothek integriert, kann der Java-Programmierer und -Nutzer diese Features in sicherer und Plattform-unabhängiger Weise nutzen.  
  *Quelle: [Wikipedia, JNI](https://de.wikipedia.org/wiki/Java_Native_Interface)*  
 
 
 ## Temperaturmessung  
 
 Nach der  Wiederholung von Java programmierten wir an unserem Program für die Temperaturmessung über eine serielleSchnitstelle weiter.  
 ### Quellcodes der ausprogrammierten Teile  
 #### MySingleMeasurementWorker  
 Hier wird eine innere Klasse erstellt. Sie übergibt eine Attribute-Variable an die eigentliche Worker Klasse.  
 ```java
  {

    public MySingleMeasurementWorker (SerialPort serialPort)
    {
      super(serialPort);
    }
    @Override
    protected void done ()
    {
      try
      {
        double temp = get();
        jlaTemperatur.setText(String.format("%.1f °C", temp));
      }
      catch (Exception ex)
      {
         showException("Schnittstelle kann nicht geöffnet werden", ex);
      }
    }
 }
  
  ```  
  #### jbutSingleMeasurementActionPerformed  
   In dieser Methode wird von der Klasse **MySingleMeasurementWorker**  ein Objekt erstellt. Dies wird mittels **execute()** gemacht.  
   
   ```java
  private void jbutSingleMeasurementActionPerformed(java.awt.event.ActionEvent evt)                       
  {                                                          
    new MySingleMeasurementWorker(serialPort).execute();
  }                                                     
```  

#### SingleMeasurementWorker Worker-Klasse  
Die Abfrage der GUI läuft mittels Swing Worker in einem anderen Thread  ab. In der Methode **doInBackground** wird eine Anfrage auf das SureBoard gesendet. 
  
```java
package workers;

import javax.swing.SwingWorker;
import jssc.SerialPort;


public class SingleMeasurementWorker extends SwingWorker<Double, Object>
{
  private final jssc.SerialPort serialPort;

  public SingleMeasurementWorker (SerialPort serialPort)
  {
    this.serialPort = serialPort;
  }

  @Override
  protected Double doInBackground () throws Exception
  {
    serialPort.writeInt(2); // Geraeteadresse
    serialPort.writeInt(4); // Funktioncode 0x04 = Read Input Register
    serialPort.writeInt(0); // LM75A Register
    serialPort.writeInt(0x30); // LM75A Register
    serialPort.writeInt(1); // Anzahl der Register
    serialPort.writeInt(0x31); // CRC Pruefsumme High-Byte
    serialPort.writeInt(0xf6); // CRC Pruefsumme Low-Byte
    // to do use Array to send data: serialPort.writeIntArray(buffer)
    
    return 23.5;
  }

}
```
