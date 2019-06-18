# Java中常量Map、常量List

```java
public static final Map<String, String> myMap = Collections.unmodifiableMap(new HashMap<String, String>() {
        private static final long serialVersionUID = 1L;

        {
            put("1", "11");
            put("2", "22");
        }
    });

    public static final List<String> myList = Collections.unmodifiableList(new ArrayList<String>() {
        private static final long serialVersionUID = 1L;

        {
            add("a");
            add("b");
        }
    });

```

