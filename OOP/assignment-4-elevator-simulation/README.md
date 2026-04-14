# Odev 4 - Asansor Simulasyonu

Bu odevde, 12 katli bir ofis binasindaki bes asansorun calismasini modelleyen bir sinif diyagrami tasarlanmistir. Tasarimda nesne yonelimli programlamanin temel ilkeleri olan encapsulation, inheritance, polymorphism ve abstraction kullanilmistir.

## Problem Ozeti

Sistemin karsilamasi gereken temel gereksinimler sunlardir:

- Binada 12 kat ve 5 asansor bulunur.
- Her asansor 12 kata da hizmet verebilir.
- Her asansorun yaklasik 6 yetiskin yolcu kapasitesi vardir.
- Asansorler sadece gerektiginde hareket ederek enerji tasarrufu saglar.
- Her asansorun kendi kapisi, kat gostergesi ve kontrol paneli vardir.
- Kontrol panelinde hedef kat dugmeleri, kapi acma, kapi kapama ve acil durum dugmesi bulunur.
- Her katta asansor bosluklari icin kapi, varis zili ve yon bilgisini gosteren sinyal isigi vardir.
- Yolcular, yukari veya asagi cagrisi ile asansor ister.
- Bir kontrol mekanizmasi, uygun asansoru secip ilgili kata yonlendirir.
- Simulasyonda zaman akisi bir saat ile izlenir.
- Olaylar zaman damgali olarak loglanir.
- Yolcu olusturma ve yolculuk bilgileri rastgele sayi uretimi ile saglanir.

## Tasarim Yaklasimi

- `Building` sinifi binayi, katlari ve asansorleri bir arada tutar.
- `ElevatorSystem` sinifi simulasyonun merkezidir; saat, loglama, yolcu uretimi ve asansor kontrolunu koordine eder.
- `ElevatorController` sinifi gelen cagrilar icin uygun asansoru secer.
- `Elevator` sinifi bir asansorun durumunu, yonunu, mevcut katini, kapasitesini ve hedeflerini yonetir.
- `Floor` sinifi kat bilgisini ve katta bulunan cagri paneli ile asansor erisimlerini temsil eder.
- `FloorElevatorAccess` sinifi, bir kat ile belirli bir asansor boslugu arasindaki kapi, zil ve yon isigini modeller.
- `Passenger` sinifi yolcunun baslangic ve hedef kat bilgisini tutar.
- `TripRequest` sinifi bir yolcunun olusturdugu asansor cagrisini temsil eder.
- `CabinPanel` ve `HallCallPanel` siniflari farkli panel tiplerini temsil eder.
- `Button` soyut sinifi, farkli dugme tipleri icin ortak bir soyutlama saglar.
- `CallButton`, `FloorSelectionButton`, `DoorOpenButton`, `DoorCloseButton` ve `EmergencyButton` siniflari polimorfik buton modelleridir.
- `SimulationClock`, `EventLogger` ve `RandomPassengerGenerator` siniflari simulasyon altyapisini destekler.

Bu tasarim, alan nesneleri ile simulasyon altyapisini ayirir. Boylece sistem hem gercek dunyadaki asansor modeline yakin kalir hem de genisletilebilir olur.

## Gorsel Ozet

![Asansor Simulasyonu Sinif Diyagrami](./elevator-simulation-diagram.svg)

```mermaid
flowchart TD
    Building[Building]
    ElevatorSystem[ElevatorSystem]
    ElevatorController[ElevatorController]
    Elevator[Elevator]
    Floor[Floor]
    Passenger[Passenger]
    TripRequest[TripRequest]
    Clock[SimulationClock]
    Logger[EventLogger]
    Generator[RandomPassengerGenerator]

    ElevatorSystem --> Building
    ElevatorSystem --> ElevatorController
    ElevatorSystem --> Clock
    ElevatorSystem --> Logger
    ElevatorSystem --> Generator
    Generator --> Passenger
    Passenger --> TripRequest
    ElevatorController --> Elevator
    Building --> Floor
    Building --> Elevator
    TripRequest --> ElevatorController
```

## Sinif Diyagrami

```mermaid
classDiagram
    class ElevatorSystem {
        +startSimulation(): void
        +step(): void
        +assignRequest(request: TripRequest): void
    }

    class Building {
        +name: String
        +floorCount: int
    }

    class Floor {
        +number: int
    }

    class FloorElevatorAccess {
        +shaftNumber: int
    }

    class ElevatorController {
        +selectElevator(request: TripRequest): Elevator
    }

    class Elevator {
        +id: String
        +capacity: int
        +currentFloor: int
        +direction: Direction
        +status: ElevatorStatus
        +move(): void
        +stop(): void
        +openDoor(): void
        +closeDoor(): void
        +addDestination(floor: int): void
    }

    class Passenger {
        +id: String
        +originFloor: int
        +destinationFloor: int
    }

    class TripRequest {
        +requestTime: DateTime
        +direction: Direction
    }

    class CabinPanel {
    }

    class HallCallPanel {
    }

    class Button {
        <<abstract>>
        +label: String
        +press(): void
    }

    class CallButton {
        +direction: Direction
    }

    class FloorSelectionButton {
        +targetFloor: int
    }

    class DoorOpenButton {
    }

    class DoorCloseButton {
    }

    class EmergencyButton {
    }

    class ElevatorDoor {
        +state: DoorState
        +open(): void
        +close(): void
    }

    class ArrivalBell {
        +ring(): void
    }

    class DirectionIndicator {
        +direction: Direction
        +turnOn(): void
        +turnOff(): void
    }

    class FloorIndicator {
        +currentFloor: int
        +update(floor: int): void
    }

    class SimulationClock {
        +currentTime: DateTime
        +tick(): void
    }

    class EventLogger {
        +log(eventText: String): void
    }

    class RandomPassengerGenerator {
        +generatePassenger(): Passenger
    }

    class ElevatorStatus {
        <<enumeration>>
        IDLE
        MOVING
        STOPPED
        MAINTENANCE
    }

    class Direction {
        <<enumeration>>
        UP
        DOWN
        NONE
    }

    class DoorState {
        <<enumeration>>
        OPEN
        CLOSED
    }

    Button <|-- CallButton
    Button <|-- FloorSelectionButton
    Button <|-- DoorOpenButton
    Button <|-- DoorCloseButton
    Button <|-- EmergencyButton

    ElevatorSystem "1" o-- "1" ElevatorController : coordinates
    ElevatorSystem "1" o-- "1" SimulationClock : uses
    ElevatorSystem "1" o-- "1" EventLogger : logs
    ElevatorSystem "1" o-- "1" RandomPassengerGenerator : creates passengers
    ElevatorSystem "1" --> "1" Building : simulates

    Building "1" o-- "12" Floor : contains
    Building "1" o-- "5" Elevator : contains

    Floor "1" o-- "3" HallCallPanel : has
    Floor "1" o-- "5" FloorElevatorAccess : has

    FloorElevatorAccess "1" o-- "1" ElevatorDoor : outer door
    FloorElevatorAccess "1" o-- "1" ArrivalBell : bell
    FloorElevatorAccess "1" o-- "1" DirectionIndicator : signal

    Elevator "1" o-- "1" ElevatorDoor : cabin door
    Elevator "1" o-- "1" CabinPanel : control panel
    Elevator "1" o-- "1" FloorIndicator : position light

    HallCallPanel "1" o-- "1..2" CallButton : contains
    CabinPanel "1" o-- "0..*" FloorSelectionButton : contains
    CabinPanel "1" o-- "1" DoorOpenButton : contains
    CabinPanel "1" o-- "1" DoorCloseButton : contains
    CabinPanel "1" o-- "1" EmergencyButton : contains

    Passenger "1" --> "1" TripRequest : creates
    TripRequest "1" --> "1" Floor : starts from
    ElevatorController "1" --> "0..*" Elevator : manages
    ElevatorController "1" --> "0..*" TripRequest : assigns
```

## Iliskilerin Aciklamasi

- `ElevatorSystem`, simulasyonun merkezi orkestrasyon sinifidir.
- `Building`, 12 kati ve 5 asansoru composition iliskisi ile tutar.
- `ElevatorController`, gelen `TripRequest` nesnelerini uygun `Elevator` ile eslestirir.
- `Elevator`, kendi kapisi, kabin paneli ve kat gostergesi ile kapsullenmis bir yapi sunar.
- `Floor`, hem cagri panellerine hem de her asansor boslugu icin ayri erisim nesnelerine sahiptir.
- `Button` soyut sinifi ve alt siniflari inheritance ve polymorphism kullanimini gosterir.
- `SimulationClock`, `EventLogger` ve `RandomPassengerGenerator`, simulasyonun teknik altyapisini saglar.

## Kisa Sonuc

Bu tasarimda:

- Gercek dunya nesneleri ayri siniflar halinde modellenmistir.
- Dugmeler icin soyutlama ve kalitim kullanilmistir.
- Kontrol, loglama ve zaman yonetimi altyapi siniflarina ayrilmistir.
- Yeni asansor politikalarini veya yeni panel tiplerini eklemek kolaylastirilmistir.

Bu sinif diyagrami, asansor simulasyonunu hem alan modeli hem de simulasyon bilesenleriyle birlikte anlasilir bir bicimde ortaya koyar.
