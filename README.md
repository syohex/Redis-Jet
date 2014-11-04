# NAME

Redis::Jet - Yet another XS implemented Redis Client

# SYNOPSIS

    use Redis::Jet;
    
    my $jet = Redis::Jet->new( server => 'localhost:6379' );
    my $ret = $jet->command(qw/set redis data-server/); # $ret eq 'OK'
    my $value = $jet->command(qw/get redis/); # $value eq 'data-server'
    
    my $ret = $jet->command(qw/set memcached cache-server/);
    my $values = $jet->command(qw/mget redis memcached mysql/);
    # $values eq ['data-server','memcached',undef]
    
    ## error handling
    ($values,$error) = $jet->command(qw/get redis memcached mysql/);
    # $error eq q!ERR wrong number of arguments for 'get' command!

    ## pipeline
    my @values = $jet->pipeline([qw/get redis/],[qw/get memcached/]);
    # \@values = [['data-server'],['cache-server']]

    my @values = $jet->pipeline([qw/get redis/],[qw/get memcached mysql/]);
    # \@values = [['data-server'],[undef,q!ERR wrong...!]]

# DESCRIPTION

This project is still in a very early development stage.
IT IS NOT READY FOR PRODUCTION!

Redis::Jet is yet another XS implemented Redis Client. This module provides
simple interfaces to communicate with Redis server

# METHODS

- `my $obj = Redis::Jet->new(%args)`

    Create a new instance.

    - `server => "server:port"`

        server address and port

    - connect\_timeout

        Time seconds to wait for establish connection. default: 5

    - io\_timeout

        Time seconds to wait for send request and read response. default: 1

    - utf8

        If enabled, Redis::Jet does encode/decode strings. default: 0 (false)

    - noreply

        IF enabled. The instance does not parse any responses. Every responses to be `"0 but true"`. default: 0 (false)

    - reconnect\_attempts

        If Redis::Jet could not connect to redis server or failed to write requests, Redis::Jet attempts to re-connect. This parameter specify how many times to try reconnect. default: 0 (disable reconnect feature);

    - reconnect\_delay

        Redis::Jet inserts delay before reconnect redis-server (see `reconnect_attempts`). default: 0.1 (100msec)

- `($value,[$error]) = $obj->command($command,$args,$args)`

    send a command and retrieve a value

- `@values = $obj->pipeline([$command,$args,$args],[$command,$args,$args])`

    send several commands and retrieve values. each value has value and error string if error was occurred.

# BENCHMARK

    single get =======
                Rate   redis    fast hiredis     jet
    redis    46036/s      --    -58%    -70%    -75%
    fast    110682/s    140%      --    -29%    -39%
    hiredis 155172/s    237%     40%      --    -15%
    jet     181695/s    295%     64%     17%      --
    single incr =======
                Rate   redis    fast hiredis     jet
    redis    49118/s      --    -58%    -70%    -73%
    fast    116294/s    137%      --    -29%    -37%
    hiredis 164938/s    236%     42%      --    -11%
    jet     184497/s    276%     59%     12%      --
    pipeline =======
              Rate redis  fast   jet
    redis  15754/s    --  -73%  -87%
    fast   58519/s  271%    --  -53%
    jet   124185/s  688%  112%    --
    
    Physical server
    Intel Xeon CPU E3-1240 v3 @ 3.40GHz | 4core/8thread    
    redis-2.8.17
    Perl-5.20.1
    Redis 1.976
    Redis::Fast 0.13
    Redis::hiredis 0.11.0

# SEE ALSO

\* [Redis](https://metacpan.org/pod/Redis)

\* [Redis::Fast](https://metacpan.org/pod/Redis::Fast)

\* [Redis::hiredis](https://metacpan.org/pod/Redis::hiredis)

\* http://redis.io/

# LICENSE

Copyright (C) Masahiro Nagano.

This library is free software; you can redistribute it and/or modify
it under the same terms as Perl itself.

# AUTHOR

Masahiro Nagano <kazeburo@gmail.com>
