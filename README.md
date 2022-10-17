# JVM. Организация памяти, сборщики мусора, VisualVM

> Задание:  просмотрите код ниже и опишите (текстово или с картинками) каждую
> строку с точки зрения происходящего в JVM
> 
> Не забудьте упомянуть про:
> 
>  - ClassLoader’ы
>  - области памяти (стэк (и его фреймы), heap)
>  - сборщик мусора



    public static void main(String[] args) {
        int i = 1;                      // 1
        Object o = new Object();        // 2
        Integer ii = 2;                 // 3
        printAll(o, i, ii);             // 4
        System.out.println("finished"); // 7
    }
    
    private static void printAll(Object o, int i, Integer ii) {
        Integer uselessVar = 700;                   // 5
        System.out.println(o.toString() + i + ii);  // 6
    }

#  Происходящее в JVM построчно
Класс JvmComprehension подгружается в ClassLoading по очередности Application ClassLoader --> Platform ClassLoader --> Bootstrap Classloader, происходит связывание (Linking) объектов и в конечном итоге происходит инициализация (вначале статичных полей , далее создаются объекты).  

**Входная точка, метод Main**, в JVM создаётся frame (кадр) в стеке (он же тред).
 1. создается локальная переменная с примитивным числовым типом данных `"i"` со значением `"1"`находясь целиком в стеке ; 
 2. интерпретируется (создается) локальная новая ссылка на объект `"o"` в стеке, сам объект помещается в heap (кучу);
 3. интерпретируется (создается) локальная новая ссылка  на объект `Integer` с наименованием `"ii"` и числовым значением `"2"` в стеке, сам объект помещается в heap (кучу);
 4. вызов статического метода `printAll` и передача в его параметры созданные ранее  ссылки на объекты `"o"` и `"ii"` и примитивную переменную `"i"`;
 
**Статический метод printAll**, в JVM создаётся frame (кадр) в стеке (он же тред).
 
 5. интерпретируется (создается) локальная новая ссылка  на объект `Integer` с наименованием `uselessVar`  и числовым значением `700` в стеке, сам объект помещается в heap (кучу);
6. происходит обращение к классу `System` (вызов метода`PrintStream`) куда передаются в качестве параметров ссылка на объект `"o"`, примитивным числовым значением `"i"`, ссылкой на объект "`ii`". В JVM создаётся frame (кадр) в стеке (он же тред) с переданными ему параметрами:
- ссылкой на объект`"о"` , которая находиться к куче;
- числовым значением `"1"` в стеке frame (кадра) Main;
- ссылка  на объект `Integer` с наименованием `"ii"` и числовым значением `"2"`.

**Сборщик мусора**

1. На первом этапе удалит удалит ссылку на объект `Integer` с наименованием `uselessVar`  и числовым значением `700`т.к. она мертвая и связь цепи GC ROOTS отсутствуют.
2. Ссылка на объект`"о"` , которая находиться к куче с числовым значением `"1"` в стеке frame (кадра) Main и ссылка  на объект `Integer` с наименованием `"ii"` и числовым значением `"2"` будут последующими т.к. вызываются в стеке frame (кадра) `Main`.
