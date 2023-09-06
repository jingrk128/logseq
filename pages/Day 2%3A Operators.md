- 原本我的code是這樣寫
- ```
      double tip_percent_double = tip_percent/100.0;
      double tax_percent_double = tax_percent/100.0;
      double result = meal_cost + (meal_cost*tip_percent_double) + (tax_percent*tax_percent_double);
      
      printf("%ld", lround(result));
  ```
- 結果只有test case3不會過
- 但是，仔細一看，我在上面的第三行最後
- 把`(meal_cost*tax_percent_double)`打成`(tax_percent*tax_percent_double)`了
- 改正之後，程式碼如下，test case3也通過了
- ```
      double tip_percent_double = tip_percent/100.0;
      double tax_percent_double = tax_percent/100.0;
      double result = meal_cost + (meal_cost*tip_percent_double) + (meal_cost*tax_percent_double);
      
      printf("%ld", lround(result));
  ```