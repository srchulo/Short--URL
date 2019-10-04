# NAME

Short::URL - Encodes and Decodes short urls by using Bijection

# SYNOPSIS

       use Short::URL;
       
       #use default alphabet
       my $su = Short::URL->new;

       my $encoded = $su->encode(10000);
       my $short_url = "http://www.example.com/$encoded";

       print "Encoded: $encoded\n"; #prints cP6
       print "Short url: $short_url\n"; #prints http://www.example.com/cP6

       my $decoded = $su->decode('cP6');

       print "Decoded: $decoded\n"; #prints 10000

       #set new alphabet
       $su->alphabet([qw/1 2 3 a b c/]);

       #or when you create the Short::URL object
       my $su = Short::URL->new(alphabet => [qw/1 2 3 a b c/]);

       #have an 'offset'. Meaning if you pass in integer $i, you get unique string for $i + offset
       $su->offset(10000);

       #or
       my $su = Short::URL->new(offset => 10000);

       my $encoded_with_offset = $su->encode(0);

       print "Encoded with offset: $encoded_with_offset\n"; #prints unique string for 10000, 'cP6'

       my $decoded_with_offset = $su->decode('cP6');

       print "Decoded with offset: $decoded_with_offset\n"; #prints 0

       #use shuffled alphabet
       $su->use_shuffled_alphabet(1);

       #or
       my $su = Short::URL->new(use_shuffled_alphabet => 1);

       my $encoded_with_shuffled_alphabet = $su->encode(10000);

       print "Encoded with shuffled alphabet: $encoded_with_shuffled_alphabet"; #prints 'dlu'

       my $decoded_with_shuffled_alphabet = $su->decode('dlu');

       print "Decoded with shuffled alphabet: $decoded_with_shuffled_alphabet"; #prints 10000

       #use no naughty words
       $su->no_vowels(1);

       #or
       my $su = Short::URL->new(no_vowels => 1);

       my $clean_encoded = $su->encode(7465182);

       print "My clean word: $clean_encoded\n"; #prints 'ccgZn', instead of 'F_ck', where '_' is replaced with 'u'
    

# DESCRIPTION

Short::URL can be used to help generate short, unique character string urls. It uses [Bijection](http://en.wikipedia.org/wiki/Bijection) to
create a one-to-one mapping from integers to strings over your alphabet, and from strings over your alphabet back to the original integer. An integer
primary key in your database would be a good example of an integer you could use to generate a unique character string that maps uniquely to that row.

# METHODS

## alphabet

    $su->alphabet([qw/1 2 3 a b c/]);

The alphabet that will be used for creating strings when mapping an integer to a string. The default alphabet is:

    [qw/a b c d e f g h i j k l m n o p q r s t u v w x y z A B C D E F G H I J K L M N O P Q R S T U V W X Y Z 0 1 2 3 4 5 6 7 8 9/]

All lower case letters, upper case letters, and digits 0-9.

## encode

    my $encoded = $su->encode(1);

Takes an integer and encodes it into a unique string over your alphabet.

## decode

    my $decoded = $su->decode('b');

Takes a string made from your alphabet and returns the corresponding integer that it maps to.

## offset

    $su->offset(10000);
    my $encoded_with_offset = $su->encode(0);
     
    print "Encoded with offset: $encoded_with_offset\n"; #prints unique string for 10000, 'cP6' instead of 'a'
     
    my $decoded_with_offset = $su->decode('cP6');
     
    print "Decoded with offset: $decoded_with_offset\n"; #prints 0 instead of 10000

["offset"](#offset) allows you to shift all integers that you encode forward by a certain integer. Likewise, whenever you are decoding strings it will
return the integer for that string by substracting your offset from what the original number would have been. Offset can be useful if you don't want people
to know you have a small number of items by starting with a larger offset, or if you want them to have less of an idea what ids really correspond to what strings.
When used in combination with ["use\_shuffled\_alphabet"](#use_shuffled_alphabet), it is a lot harder to track what string would correspond
to what id in your database, or how many ids you have in total. 

## use\_shuffled\_alphabet

    $su->use_shuffled_alphabet(1);

    my $encoded_with_shuffled_alphabet = $su->encode(10000);

    print "Encoded with shuffled alphabet: $encoded_with_shuffled_alphabet"; #prints 'dlu'

Setting ["use\_shuffled\_alphabet"](#use_shuffled_alphabet) to 1 means that instead of using the more common alphabet used for this algorithm that is stored in ["alphabet"](#alphabet), [Short::URL](https://metacpan.org/pod/Short::URL) will use a shuffled alphabet.
Note, this shuffled alphabet will be the same every time, so encoding and decoding will work even with the use\_shuffled\_alphabet set to 1 even in 
different sessions of using [Short::URL](https://metacpan.org/pod/Short::URL). This can be 
useful if for some reason you don't want people to know how many ids you have in your database. With the more standard alphabet it's clear when you are going
up by one between strings, and thus also how many ids you have. When used in combination with ["offset"](#offset), it is a lot harder to track what string would correspond
to what id in your databse, or how many ids you have in total. Below is the shuffled alphabet that is used:

    [qw/G w d A t H J 0 P o W C 6 3 y K 8 L u 7 X E O a e q 1 9 D c F x Z 5 M T N l z r i s j h B 4 b I f V Y Q g n S 2 m U k p v R/]

This is just a shuffled version of ["alphabet"](#alphabet). The default for ["use\_shuffled\_alphabet"](#use_shuffled_alphabet) is undef.

## no\_vowels

As pointed out [here](http://stackoverflow.com/questions/742013/how-to-code-a-url-shortener/742047#comment25208796_742047), there is the possibility of creating
naughty words. For instance:

    my $encoded = $su->encode(7465182);

    print "Encoded naughty: $encoded\n"; #prints F_ck, where '_' is replaced with 'u'

If you want to avoid this, you can set ["no\_vowels"](#no_vowels) to 1:

    $su->no_vowels(1);

    #or
    my $su = Short::URL->new(no_vowels => 1);

This uses a separate alphabet with no vowels in it (aeiou), so that no naughty words can be formed. If you also have ["use\_shuffled\_alphabet"](#use_shuffled_alphabet) set to 1, that will still be
respected. [Short::URL](https://metacpan.org/pod/Short::URL) will just use a clean version of the shuffled alphabet listed under ["use\_shuffled\_alphabet"](#use_shuffled_alphabet). Below are the two clean alphabets:

    #clean_alphabet
    [qw/b c d f g h j k l m n p q r s t v w x y z B C D F G H J K L M N P Q R S T V W X Y Z 0 1 2 3 4 5 6 7 8 9/]

    #clean_shuffled_alphabet
    [qw/G w d t H J 0 P W C 6 3 y K 8 L 7 X q 1 9 D c F x Z 5 M T N l z r s j h B 4 b f V Y Q g n S 2 m k p v R/]

## croak\_on\_error

This method sets whether you want [Short::URL](https://metacpan.org/pod/Short::URL) to ["croak"](https://metacpan.org/pod/Carp) on an error.

    #enable
    $su->croak_on_error(1);

    #disable
    $su->croak_on_error(0);

Default is 1. If ["croak\_on\_error"](#croak_on_error) is set to false and there is an error, then ["encode"](#encode) or ["decode"](#decode) will return a negative number.

# AUTHOR

Adam Hopkins <srchulo@cpan.org>

# COPYRIGHT

Copyright 2019- Adam Hopkins

# LICENSE

This library is free software; you can redistribute it and/or modify
it under the same terms as Perl itself.

# SEE ALSO

- [http://stackoverflow.com/a/742047/834140](http://stackoverflow.com/a/742047/834140)
- [https://gist.github.com/zumbojo/1073996](https://gist.github.com/zumbojo/1073996)
