
### test javascript

```
##
        START TRANSACTION;
        call generate_all_random_reservation_status(1);
        call generate_all();
        select * from reservation;
        select * from fine;
        select * from payment;
        select * from finestructure;
        ROLLBACK; -- COMMIT: IF NO PROBLEM THEN CHANGE THIS LINE TO COMMIT!
```

