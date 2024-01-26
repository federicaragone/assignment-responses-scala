# assignment-responses-scala
**Question 1**

Find the total number of flights for each month.

```scala
import com.opencsv.CSVReaderBuilder
import java.time.LocalDate
import scala.jdk.CollectionConverters._

object Q1 {

  def main(args: Array[String]): Unit = {
    val flightData = """C:\\Users\\feder\\OneDrive\\Desktop\\quantexa\\flightData.csv"""
    val dateColumnName = "date"

    val flightsByMonth = readExcel(flightData, dateColumnName)
    printResults(flightsByMonth)
  }

  def readExcel(filePath: String, dateColumnName: String): Map[Int, Int] = {
    val reader = new CSVReaderBuilder(new java.io.FileReader(filePath)).withSkipLines(1).build()
    val rows = reader.readAll().asScala.toList

    val dateColumnIndex = rows.head.indexOf(dateColumnName) + 5
    val idColumnIndex = 1

    val flightsByMonth = rows
      .map(row => (getMonthFromDate(row(dateColumnIndex)), row(idColumnIndex)))
      .distinct
      .groupBy(_._1)
      .view.mapValues(_.size)
      .toMap

    (1 to 12).map(month => month -> flightsByMonth.getOrElse(month, 0)).toMap
  }

  def getMonthFromDate(dateStr: String): Int = {
    LocalDate.parse(dateStr).getMonthValue
  }

  def printResults(flightsByMonth: Map[Int, Int]): Unit = {
    printf("%-10s%-15s%n", "Month", "Total Flights")
    (1 to 12).foreach { month =>
      val count = flightsByMonth.getOrElse(month, 0)
      printf("%-10d%-15d%n", month, count)
    }
  }
}
```
OUTPUT
Month     Total Flights  
1         97             
2         73             
3         82             
4         92             
5         92             
6         71             
7         87             
8         76             
9         85             
10        76             
11        75             
12        94             

**Question 2**

Find the names of the 100 most frequent flyers.

```scala
import com.opencsv.CSVReaderBuilder
import scala.jdk.CollectionConverters._

object Q2 {

  def main(args: Array[String]): Unit = {
    val flightData = "C:\\Users\\feder\\OneDrive\\Desktop\\quantexa\\flightData.csv"
    val passengerData = "C:\\Users\\feder\\OneDrive\\Desktop\\quantexa\\passengers.csv"
    val dateColumnName = "date"

    val topFrequentFlyers = findTopFrequentFlyers(flightData, passengerData, dateColumnName, 100)
    printFrequentFlyers(topFrequentFlyers)
  }

  def findTopFrequentFlyers(flightFilePath: String, passengerFilePath: String, dateColumnName: String, limit: Int): List[(String, Int, String, String)] = {
    val flightReader = new CSVReaderBuilder(new java.io.FileReader(flightFilePath)).withSkipLines(1).build()
    val passengerReader = new CSVReaderBuilder(new java.io.FileReader(passengerFilePath)).withSkipLines(1).build()

    val flightRows = flightReader.readAll().asScala.toList
    val passengerRows = passengerReader.readAll().asScala.toList

    val passengerIdIndex = 0

    val frequentFlyers = flightRows
      .groupBy(row => row(passengerIdIndex))
      .view.mapValues(_.size)
      .toList
      .sortBy { case (_, count) => -count }
      .take(limit)

    frequentFlyers.flatMap { case (passengerId, flightCount) =>
      val passengerInfo = passengerRows.find(row => row(passengerIdIndex) == passengerId)
      val (firstName, lastName) = passengerInfo.map(row => (row(1), row(2))).getOrElse(("Unknown", ""))
      List((passengerId, flightCount, firstName, lastName))
    }
  }

  def printFrequentFlyers(results: List[(String, Int, String, String)]): Unit = {
    printf("%-15s%-20s%-15s%-15s%n", "Passenger ID", "Number of Flights", "First name", "Last name")
    results.foreach { case (passengerId, flightCount, firstName, lastName) =>
      printf("%-15s%-20d%-15s%-15s%n", passengerId, flightCount, firstName, lastName)
    }
  }
}
```

OUTPUT

Passenger ID   Number of Flights   First name     Last name      
2068           32                  Yolande        Pete           
1677           27                  Katherina      Vasiliki       
4827           27                  Jaime          Renay          
8961           26                  Ginny          Clara          
3173           26                  Sunshine       Scott          
2857           25                  Son            Ginette        
917            25                  Anisha         Alaine         
5867           25                  Luise          Raymond        
8363           25                  Branda         Kimiko         
6084           25                  Cole           Sharyl         
760            25                  Vernia         Mui            
288            25                  Pamila         Mavis          
5096           25                  Blythe         Hyon           
1240           24                  Catherine      Missy          
1343           24                  Bennett        Staci          
5668           24                  Gladis         Earlene        
2441           24                  Kayla          Rufus          
3367           24                  Priscilla      Corie          
4056           23                  Dagmar         Marilee        
1673           23                  Jeanie         Gladis         
1598           23                  Amelia         Betsey         
3405           23                  Johna          Ollie          
5332           23                  Bobette        Laurice        
613            23                  Palmer         Yuonne         
2717           23                  Shannon        Marlyn         
8353           23                  Davis          Williams       
281            23                  Sage           Fernanda       
7643           23                  Albina         Joni           
2251           23                  Gussie         Temple         
3093           23                  Theda          Rodrick        
8411           23                  Juliann        Cheryll        
1337           23                  Kirby          Melvin         
9441           23                  Annalisa       Luanna         
211            23                  Lori           Jeremiah       
3590           23                  Isiah          Tyler          
366            23                  Carman         Meghan         
3180           22                  Yvette         Peter          
5490           22                  Toni           Winnifred      
798            22                  Ramiro         Terisa         
7162           22                  Alfonso        Dinah          
2437           22                  Charlyn        Wynona         
1769           22                  Lisha          Delmer         
739            22                  Sadye          Rhea           
4611           22                  Cristi         Eugenia        
392            22                  Cory           Ebonie         
350            22                  Annemarie      Nilsa          
4034           22                  Thalia         Maurice        
3078           22                  Laquita        Kate           
5423           22                  Sirena         Nigel          
391            22                  Carolynn       Enola          
6447           22                  Brigitte       Jodie          
2939           22                  Jaleesa        Clement        
167            22                  Mona           Kassandra      
258            22                  Emmaline       Marcelle       
9252           22                  Angella        Margret        
1413           22                  Carlee         Timmy          
5416           21                  Chin           Claretta       
4632           21                  Jacklyn        Christia       
1464           21                  Margherita     Arica          
2254           21                  Angelina       Gracia         
6060           21                  Delisa         Elisha         
4249           21                  Chi            Valeri         
1982           21                  Izetta         Marjorie       
6777           21                  Tamara         Terresa        
1581           21                  Marx           Artie          
437            21                  Kelli          Roger          
9540           21                  Rhoda          Gavin          
848            21                  Marya          Eliana         
398            21                  Mario          Trinh          
4399           21                  Kevin          Nicole         
364            21                  Donya          Stephan        
2185           21                  Rae            Chas           
5560           21                  Lang           Johnie         
3332           21                  Kiara          Donnie         
4552           21                  Mayme          Luvenia        
5007           21                  Laticia        Katherine      
3112           21                  Madie          Winona         
435            21                  Tiffani        Frances        
905            21                  Terrie         Gisela         
763            21                  Will           Chanelle       
2926           21                  Vicky          Patrica        
1651           21                  Alex           Annabelle      
4502           21                  Thea           Yolando        
2516           21                  Tamar          Claretta       
7810           20                  Willie         Glen           
5775           20                  Mozella        Essie          
3589           20                  Lee            Ben            
4774           20                  Myles          Ivan           
1617           20                  Cody           Lashell        
3021           20                  Ervin          Milda          
3883           20                  Vina           Jewel          
649            20                  Doreen         Raymond        
3802           20                  Gwyn           Cherish        
2734           20                  Otha           Genoveva       
721            20                  Yun            Cecily         
2511           20                  Verona         Bobbie         
139            20                  Vita           Eileen         
2378           20                  Berenice       Eladia         
3937           20                  Palmer         Marisela       
1246           20                  Shaquita       Evon           
  
**Question 3**

Find the greatest number of countries a passenger has been in without being in the UK. For example, if the countries a passenger was in were: UK -> FR -> US -> CN -> UK -> DE -> UK, the correct answer would be 3 countries.

Order the output on Q3 by 'longest run in descending order'.
```scala
import com.opencsv.CSVReaderBuilder
import scala.jdk.CollectionConverters._

object Q3 {

  case class Flight(passengerId: String, from: String, to: String)

  def main(args: Array[String]): Unit = {
    val flightData = """C:\\Users\\feder\\OneDrive\\Desktop\\quantexa\\flightData.csv"""
    val passengerData = """C:\Users\feder\OneDrive\Desktop\quantexa\passengers.csv"""

    val longestRuns = findLongestRuns(flightData, passengerData)
    printLongestRuns(longestRuns)
  }

  def findLongestRuns(flightFilePath: String, passengerFilePath: String): Map[String, Int] = {
    val flightReader = new CSVReaderBuilder(new java.io.FileReader(flightFilePath)).withSkipLines(1).build()
    val passengerReader = new CSVReaderBuilder(new java.io.FileReader(passengerFilePath)).withSkipLines(1).build()

    val flightRows = flightReader.readAll().asScala.map(row => Flight(row(0), row(2), row(3))).toList
    val passengerRows = passengerReader.readAll().asScala.toList

    val passengerIdIndex = 0

    val runsByPassenger = flightRows
      .groupBy(_.passengerId)
      .view.mapValues { flights =>
        val nonUKRuns = getNonUKRuns(flights.map(_.to).distinct)
        if (nonUKRuns.nonEmpty) nonUKRuns.max else 0
      }
      .toMap

    runsByPassenger
  }

  def getNonUKRuns(countries: List[String]): List[Int] = {
    countries.foldLeft((0, 0)) { case ((currentRun, longestRun), country) =>
      if (country != "UK") (if (currentRun > 0) currentRun + 1 else 1, Math.max(longestRun, currentRun))
      else (0, longestRun)
    }._2 :: Nil
  }

  def printLongestRuns(longestRuns: Map[String, Int]): Unit = {
    printf("%-15s%-15s%n", "Passenger ID", "Longest Run")
    longestRuns.toList.sortBy { case (_, runLength) => -runLength }.foreach { case (passengerId, runLength) =>
      printf("%-15s%-15d%n", passengerId, runLength)
    }
  }
}

```

(a sample of the first few lines)OUTPUT 

Passenger ID   Longest Run    
1677           17             
721            17             
9441           17             
288            17             
4632           16             
798            16             
2857           16             
2437           16             
2378           16             
8353           16             
3608           16             
8961           16             
7643           16             
2867           16             
2185           16             
6084           16             
2939           16             
5668           16             
8411           16             
2441           16             
1651           16             
2068           16             
3367           16             
157            16             
381            15             
5416           15             
6294           15             
3802           15             
7162           15             
739            15             
392            15             
437            15             
888            15             
917            15             
772            15             
8988           15             
1074           15             
3078           15             
8149           15             
628            15             
2251           15             
2262           15             
3309           15             
4827           15             
7327           15             
950            15             
525            15             
6036           15             
3117           15             
2544           15             
1337           15             
92             15             
1266           15             
942            15             
3173           15             
211            15             
2516           15             
5096           15             
1464           14             
2254           14             
4774           14             
3021           14             
3119           14             
1598           14             
362            14             
2734           14             
4249           14             
3405           14             
2518           14             
3486           14             
5332           14             
3600           14             
2747           14             
1769           14             
407            14             
4611           14             
5731           14             
773            14             
6027           14     

**Question 4**
Find the passengers who have been on more than 3 flights together.

Order the output on Q4 by 'number of flights flown together in descending order'.

```scala
import scala.collection.mutable

object Q4 {

  case class Flight(passengerId: String, flightId: Int)

  def main(args: Array[String]): Unit = {
    val filename = """C:\\Users\\feder\\OneDrive\\Desktop\\quantexa\\flightData.csv"""
    val flights: List[Flight] = readFlights(filename)

    val flightGroups: Map[Int, List[Flight]] = flights.groupBy(_.flightId)

    val combinations: mutable.Map[(String, String), Int] = mutable.Map().withDefaultValue(0)

    for {
      (_, group) <- flightGroups
      i <- group.indices
      j <- i + 1 until group.length
    } {
      val passenger1 = group(i).passengerId
      val passenger2 = group(j).passengerId
      combinations((passenger1, passenger2)) += 1
    }

    val filteredCombinations = combinations.filter(_._2 > 3)

    val sortedCombinations = filteredCombinations.toList.sortBy(-_._2)

    val maxP1Length = sortedCombinations.map(_._1._1.length).max
    val maxP2Length = sortedCombinations.map(_._1._2.length).max

    println(s"${"Passenger 1 ID".padTo(maxP1Length + 5, ' ')}${"Passenger 2 ID".padTo(maxP2Length + 5, ' ')}Number of flights together")
    sortedCombinations.foreach { case ((passenger1, passenger2), flightsTogether) =>
      println(f"${passenger1.padTo(maxP1Length + 5, ' ')}${passenger2.padTo(maxP2Length + 5, ' ')}$flightsTogether")
    }
  }

  def readFlights(filename: String): List[Flight] = {
    val bufferedSource = io.Source.fromFile(filename)
    val flights = bufferedSource.getLines().drop(1).map { line =>
      val cols = line.split(",").map(_.trim)
      Flight(cols(0), cols(1).toInt)
    }.toList
    bufferedSource.close()
    flights
  }
}
```
(a sample of the first few lines)OUTPUT 


Passenger 1 IDPassenger 2 IDNumber of flights together

752       366       9

9252      7877      9

4827      1673      9

4441      2550      9

5494      5500      9

5494      5491      9

7747      9522      9

4541      7162      9

7866      7872      9

391       1662      9

1371      975       9

701       760       9

1666      1696      9

746       1023      8

2068      7810      8

5481      5491      8

