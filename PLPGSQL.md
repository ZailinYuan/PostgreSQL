# Link
https://pg.sjk66.com/stored-procedure/loop.html

# for loop
1.

    [ <<label>> ]
    for loop_counter in [ reverse ] from.. to [ by step ] loop
        statements
    end loop [ label ];
    
2.

    do $$
    begin
       for counter in 1..5 loop
      raise notice 'counter: %', counter;
       end loop;
    end; $$

3.

    do $$
    begin 
      for counter in 1..6 by 2 loop
        raise notice 'counter: %', counter;
      end loop;
    end; $$

4.

    do
    $$
    declare
        f record;
    begin
        for f in select title, length 
               from film 
               order by length desc, title
               limit 10 
        loop 
        raise notice '%(% mins)', f.title, f.length;
        end loop;
    end;
    $$

5.

    [ <<label>> ]
    for row in execute query_expression [ using query_param [, ... ] ] 
    loop
        statements
    end loop [ label ];
    
6.

    do $$
    declare
        -- sort by 1: title, 2: release year
        sort_type smallint := 1; 
        -- return the number of films
        rec_count int := 10;
        -- use to iterate over the film
        rec record;
        -- dynamic query
        query text;
    begin

        query := 'select title, release_year from film ';

        if sort_type = 1 then
            query := query || 'order by title';
        elsif sort_type = 2 then
          query := query || 'order by release_year';
        else 
           raise 'invalid sort type %s', sort_type;
        end if;

        query := query || ' limit $1';

        for rec in execute query using rec_count
            loop
             raise notice '% - %', rec.release_year, rec.title;
        end loop;
    end;
    $$
