# ELR-Records
46 ELR-Records/Records.java 78  

Cont. from 8.7 [43.44.45.BGX-Singlet](https://github.com/Java-PJATK/43.44.45.BGX-Singlet/)

## 8.8 Records 

Quite often, we need a class whose objects represent just several pieces of data that are not supposed to be modified after the object’s creation — something like structures in pure C, but unmodifiable. This special type of class exists in Java and is called a **record**. 

Such classes are sometimes called **data classes** or **data carrier**. They contain, created automatically, one constructor, private fields and methods for accessing them (so called _getters_ or _accessors_), and three methods: `toString`, `hashCode` and `equals`. 

Objects of these classes can be treated as simply aggregates of data used by other classes; usually, they don’t contain any additional functionality. To protect immutability, they are implicitly `final`, i.e., they cannot be extended (no class can inherit from them). 7 (See Sec. 10, p. 87)

Such classes are not only useful in many contexts, but also have a very valuable feature related to their immutability: objects of such classes can safely be used in multithreaded environment without complicated and error prone synchronizations.

Let us define such a class, `Car`, with just two fields, make and model. We have to define these fields, declaring them as immutable (`final`), provide a constructor setting
values of these fields, add getters, override the `toString` method, and very often also methods `equals` and `hashCode` (we will talk about their importance in Sec. ?? p. ??).

## Listing 46 ELR-Records/Records.java  

```java 
import java.util.Objects;

public class Records {
    public static void main(String[] args) {
        Car c = new Car("Toyota", "Camry");
        System.out.println(c);

        Motorcycle m1 = new Motorcycle("Kawasaki", "Vulcan");
        Motorcycle m2 = new Motorcycle("Kawasaki", "Vulcan");
        System.out.println(m1);
        System.out.println(m1.hashCode() + " " + m2.hashCode());
        System.out.println(m1.equals(m2));
        System.out.println(m1.make() + " " + m1.model());
    }
}

final class Car {
    private final String make;
    private final String model;

    Car(String make, String model) {
        this.make  = make;
        this.model = model;
    }

    @Override
    public int hashCode() {
        return Objects.hash(make, model);
    }

    @Override
    public boolean equals(Object other) {
        if (this == other) return true;
        if (other == null ||
          !(other instanceof Car)) return false;
        Car c = (Car) other;
        return Objects.equals(make,  c.make) &&
               Objects.equals(model, c.model);
    }

    @Override
    public String toString() {
        return String.format("Car[make=%s, model=%s]", make, model);
    }

    public String make()  { return make; }
    public String model() { return model; }
}

record Motorcycle (String make, String model) { }
```

Notice that the class is very simple, but still requires quite a lot of “boilerplate” code.

All this can be done in just one line: look at the last line of the above program. 

Here, we define a record `Motorcycle` — instead of the `class`, we use the `record` keyword. 

It’s then enough to specify, in round parentheses, types and names of the (private) fields required (this is sometimes called the _descriptor_ of the record). Everything else will be done automatically:  

* Private, final fields will be declared, with names as given as parameters of the descriptor.
  
* The canonical constructor will be created, assigning values to all fields.

* Canonical getters 8(But not setters, as objects of records are, by definition, immutable.) will be created with names as those of the fields (without the usual get prefix!).
    
* Default implementation of the `equals`, `hashCode` and `toString` methods will be added automatically, but if we want to customize them for some reason, we can define them ourselves.

* Classes defined as records are by definition `final`.

* The direct superclass of records is `java.lang.Record`, i.e., they implicitly inherit from the abstract class `Record`.

The program prints:

```
Car[make=Toyota, model=Camry]
Motorcycle[make=Kawasaki, model=Vulcan]
-1462729257 -1462729257
true
Kawasaki Vulcan
```

The two classes, `Car` and `Motorcycle`, are essentially equivalent, but the latter took only one line to be defined! We can see it by disassembling them using the javap tool:  

```
$ javap -p Car.class Motorcycle.class
Compiled from "Records.java"
final class Car {
    private final java.lang.String make;
    private final java.lang.String model;
    Car(java.lang.String, java.lang.String);
    public int hashCode();
    public boolean equals(java.lang.Object);
    public java.lang.String toString();
    public java.lang.String make();
    public java.lang.String model();
}
Compiled from "Records.java"
final class Motorcycle extends java.lang.Record {
    private final java.lang.String make;
    private final java.lang.String model;
    Motorcycle(java.lang.String, java.lang.String);
    public final java.lang.String toString();
    public final int hashCode();
    public final boolean equals(java.lang.Object);
    public java.lang.String make();
    public java.lang.String model();
}
```

The `equals`, `toString` and `hashCode` have been implicitly declared as `final` in the `Motorcycle` record; this is not an issue, though, as the class is `final` anyway.


Next [Listing 47 ELN-Rec](https://github.com/Java-PJATK/47.ELN-Rec)

---

[![IMAGE ALT TEXT HERE](http://img.youtube.com/vi/YOUTUBE_VIDEO_ID_HERE/0.jpg)](https://www.youtube.com/watch?v=gJ9DYC-jswo&ab_channel=CodingwithJohn)
