# Dataset Notes


1. The medals file does not allow us to link medals awarded at the granularity of the athletes. We're only able to see medals by NOC.

2. there are duplicate sin the athletes and coaches dataset

    ```
    athletes_df.groupBy('Name', 'NOC', 'Discipline').count().filter('count > 1').show()
    coaches_df.groupBy('Name', 'NOC', 'Discipline', 'Event').count().filter('count > 1').show()
    medals_df.groupBy('Rank', 'Team/NOC', 'Gold', 'Silver', 'Bronze', 'Total', 'Rank by Total').count().filter('count > 1').show()
    teams_df.groupBy('Name', 'NOC', 'Discipline', 'Event').count().filter('count > 1').show()```

3. Not all athletes + discipline combinations have coaches (this seems reasonable. Can't assume every country supports every athlete)
   1. ```spark.sql("""select * from coaches where NOC = 'Hungary'""").show()```

4. No Gender for athlete. Can not discern between Gender for coaches


5. cases where we have no coach or a team but athlete won a metal.

    ```
    spark.sql("""select
    b.athlete_name,
    b.coach_name,
    b.noc,
    b.discipline,
    mnc.medal_type,
    t.team_name
    from base b
    left join medals_new_clean mnc on b.athlete_name = mnc.athlete_name and b.noc = mnc.noc and b.discipline = mnc.discipline
    left outer join teams_clean t on b.discipline = t.discipline and b.noc = t.noc
    where team_name is null
    and medal_type is not null
    order by b.athlete_name, b.noc, b.discipline
```
""")


6. Using new medals data, we're able to get a more accurate count of medals earned. The new data also has event and not just discipline. This is big with swimming in which there are many events all within "swimming"

