+++
title = "Override equals and hashCode correctly"
date = 2022-03-31

[taxonomies]
tags = ["snippet"]
language = ["java"]

[extra]
author = "Colin McCulloch"
+++

A Java classes `equals` and `hashCode` methods should always be overridden in pairs, the following shows how to do this manually without using 3rd party libraries

```java
public class Stock {
    private int tickSize;
    private long lotSize;
    private boolean isRestricted;
    private String symbol;
    private String exchange;
    private Date settlementDate;
    private BigDecimal price;
        
    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + tickSize;
        result = prime * result + (int) (lotSize ^ (lotSize >>> 32));
        result = prime * result + (isRestricted ? 1231 : 1237);
        result = prime * result + ((symbol == null) ? 0 : symbol.hashCode());
        result = prime * result + ((exchange == null) ? 0 : exchange.hashCode());
        result = prime * result + ((settlementDate == null) ? 0 : settlementDate.hashCode());
        result = prime * result + ((price == null) ? 0 : price.hashCode());
        return result;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || this.getClass() != obj.getClass()){
            return false;
        }

        Stock other = (Stock) obj;

        return  
            this.tickSize == other.tickSize && this.lotSize == other.lotSize && 
            this.isRestricted == other.isRestricted &&
            (this.symbol == other.symbol|| (this.symbol != null && this.symbol.equals(other.symbol))) && 
            (this.exchange == other.exchange|| (this.exchange != null && this.exchange.equals(other.exchange))) &&
            (this.settlementDate == other.settlementDate|| (this.settlementDate != null && this.settlementDate.equals(other.settlementDate))) &&
            (this.price == other.price|| (this.price != null && this.price.equals(other.price)));
    }
}
```

The Java `equals` contract defines that the method must be:

- **Reflexive**: An object must equal itself
- **Symetric**: if `x.equals(y)` then `y.equals(x)`
- **Transitive**: if `x.equals(y)` and `y.equals(z)` then `x.equals(z)`
- **Consistent**: The value should only change if a member used in the calculation changes i.e. no randomness.

`hashCode` calculation is related to the `equals` method in the following ways

- **Internal Consistency**: The value can only change if a member used in the `equals` calculation changes
- **Equals Consistency**: If two objects are `equal` then they must have the same `hashCode`
- **Collisions**: unequal objects *may* have the same `hashCode`, nothing in the definition prohibits this