## vehicle-rental-system

> Database Diagram: https://dbdiagram.io/d/vehicle-rental-system-6947fc8c4bbde0fd74ed6fb3
## ![Database Diagram](https://raw.githubusercontent.com/Shamiul-Lipu/db-playground/main/erd.png)

## SQL Queries

### Query 1: Retrieve booking information along with: Customer name, Vehicle name

```sql
select
    b.booking_id,
    u.name as customer_name,
    v.name as vehicle_name,
    b.start_date,
    b.end_date,
    b.status
from bookings b
    join users u using (user_id)
    join vehicles v using (vehicle_id);
```

**Explanation:**
এই query-তে আমরা bookings, users, এবং vehicles টেবিলগুলোকে JOIN দিয়ে যুক্ত করেছি। এখানে আমরা দেখতে পারছি কোন user কোন vehicle কবে বুক করেছে এবং সেই booking-এর status কী। JOIN নিশ্চিত করে যে শুধুমাত্র সেই booking দেখানো হবে যেখানে both user এবং vehicle সঠিকভাবে মিলে আছে। এটি মূলত booking information এর সাথে customer এবং vehicle এর নাম display করার জন্য ব্যবহার করা হয়।

### Query 2: Find all vehicles that have never been booked.

```sql
select *
from vehicles v
where
    not exists (
        select 1
        from bookings b
        where
            b.vehicle_id = v.vehicle_id
    );
```

**Explanation:**
এই query-তে আমরা NOT EXISTS ব্যবহার করেছি। এর মাধ্যমে আমরা এমন সব vehicles খুঁজছি যেগুলো এখনও কোনো booking এ ব্যবহার হয়নি। প্রতিটি vehicle_id এর জন্য আমরা check করছি যে সেই vehicle_id কি bookings টেবিলে আছে কি না। যদি না থাকে, তাহলে সেই vehicle select হবে। এটি আমাদের বলে দেয় কোন vehicles এখনও free এবং booking এর জন্য available।

### Query 3: Retrieve all available vehicles of a specific type (e.g. cars).

```sql
select *
from vehicles
where
    status = 'available'
    and type = 'car';
```

**Explanation:**
এই query-তে আমরা WHERE clause ব্যবহার করে vehicles filter করেছি। এখানে আমরা দেখছি নির্দিষ্ট type এর vehicles (যেমন ‘car’) যেগুলো status = available। অর্থাৎ, এটি কেবল সেই vehicles দেখাচ্ছে যেগুলো এখন booking এর জন্য ready এবং আমাদের specific type এর সাথে match করছে। এটি user কে সহজে available option দেখানোর জন্য useful।

### Query 4: Find the total number of bookings for each vehicle and display only those vehicles that have more than 2 bookings.

```sql
select v.name, count(b.booking_id) as total_bookings
from vehicles v
    join bookings b on v.vehicle_id = b.vehicle_id
group by
    v.vehicle_id,
    v.name
having
    count(b.booking_id) > 2;
```

**Explanation:**
এই query-তে আমরা GROUP BY এবং HAVING ব্যবহার করেছি। আমরা প্রতিটি vehicle অনুযায়ী total bookings count করছি। তারপর HAVING COUNT > 2 ব্যবহার করে filter করছি, যাতে শুধুমাত্র সেই vehicles দেখায় যেগুলোর bookings 2 এর বেশি। এটি আমাদের analysis-এ সাহায্য করে বুঝতে কোন vehicles বেশি use হয়েছে বা high demand এ আছে।
