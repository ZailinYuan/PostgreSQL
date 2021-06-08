# Link
https://pg.sjk66.com/stored-procedure/loop.html

# for loop
    [ <<label>> ]
    for loop_counter in [ reverse ] from.. to [ by step ] loop
        statements
    end loop [ label ];
    
    do $$
    begin
       for counter in 1..5 loop
      raise notice 'counter: %', counter;
       end loop;
    end; $$
