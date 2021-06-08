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
