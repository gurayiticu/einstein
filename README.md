# einstein

#include <iostream>
#include <algorithm>

namespace {

// the possible values of the five variables
enum nation    { british, swedish, norwegian, german, danish };
enum colour    { red, green, yellow, blue, white };
enum animal    { dog, horse, cat, bird, fish };
enum cigarette { marlboro, winfield, rothmans, pallmall, dunhill };
enum drink     { tea, coffee, milk, beer, water };

// these are for printing the results; obviously the order must correspond to the enums above
const char * nation_names[]     = { "british", "swedish", "norwegian", "german", "danish" };
const char * colour_names[]     = { "red", "green", "yellow", "blue", "white" };
const char * animal_names[]     = { "dog", "horse", "cat", "bird", "fish" };
const char * cigarette_names[]  = { "marlboro", "winfield", "rothmans", "pallmall", "dunhill" };
const char * drink_names[]      = { "tea", "coffee", "milk", "beer", "water" };

// arbitrarily assign the leftmost house an index of 0, and the rightmost 4;
// e.g. the nationality of the owner of the left-most house would be given by
// nations[0], and the pet he keeps would be given by animals[0], and the pet
// his neighbour (on his right) keeps would be given by animals[1], and so on
const int num_houses = 5;
nation    nations[num_houses];
colour    colours[num_houses];
animal    animals[num_houses];
cigarette cigarettes[num_houses];
drink     drinks[num_houses];


// set 'array' to first permutation, which is numerically { 0, 1, 2, 3, 4 }
template <class T, size_t size>
void init(T (& array)[size])
{
    for (int i = 0; i < size; ++i)
        array[i] = static_cast<T>(i);
}

// set 'array' to next permutation if possible, otherwise return false
template <class T, size_t size>
bool next_permutation(T (& array)[size])
{
    return std::next_permutation(array, array + size);
}

// set the combination of all house variables to the next permutation
bool permute()
{
    if (next_permutation(nations))
        return true;
    init(nations);
    if (next_permutation(colours))
        return true;
    init(colours);
    if (next_permutation(animals))
        return true;
    init(animals);
    if (next_permutation(cigarettes))
        return true;
    init(cigarettes);
    if (next_permutation(drinks))
        return true;
    init(drinks);
    // we have cycled through all permutations of all variables
    return false;
}

// return the number of the house of the given colour
inline int find_colour(colour c)
{
    int i = 0;
    while (colours[i] != c)
        ++i;
    return i;
}

// return the number of the house whose owner is of the given nationality
inline int find_nation(nation n)
{
    int i = 0;
    while (nations[i] != n)
        ++i;
    return i;
}

// return the number of the house whose owner smokes the given brand of cigarette
inline int find_cigarette(cigarette c)
{
    int i = 0;
    while (cigarettes[i] != c)
        ++i;
    return i;
}

// the Norwegian lives next to the blue house
inline bool norwegian_lives_next_to_blue_house()
{
    const int i = find_nation(norwegian);
    return (i > 0 && colours[i-1] == blue)
        || (i < (num_houses - 1) && colours[i+1] == blue);
}
// the green house is on the left of the white house
inline bool green_left_of_white()
{
    const int i = find_colour(green);
    return i < (num_houses - 1) && colours[i+1] == white;
}
// the person who smokes Marlboro lives next to the one who keeps cats
inline bool smokes_marlboro_next_cats()
{
    const int i = find_cigarette(marlboro);
    return (i > 0 && animals[i-1] == cat)
        || (i < (num_houses - 1) && animals[i+1] == cat);
}
// the person who keeps horses lives next to the person who smokes Dunhill
inline bool smokes_dunhill_next_horses()
{
    const int i = find_cigarette(dunhill);
    return (i > 0 && animals[i-1] == horse)
        || (i < (num_houses - 1) && animals[i+1] == horse);
}
// the person who smokes Marlboro has a neigbor who drinks water
inline bool smokes_marlboro_next_water()
{
    const int i = find_cigarette(marlboro);
    return (i > 0 && drinks[i-1] == water)
        || (i < (num_houses - 1) && drinks[i+1] == water);
}

// return true iff all the stated conditions are met
bool is_solution()
{
    return
        // the Norwegian lives in the first house
        nations[0] == norwegian
        // the man living in the centre house drinks milk
        && drinks[num_houses/2] == milk
        // the Brit lives in the red house
        && colours[find_nation(british)] == red
        // the Swede keeps dogs as pets
        && animals[find_nation(swedish)] == dog
        // the Dane drinks tea
        && drinks[find_nation(danish)] == tea
        // the German smokes Rothmans
        && cigarettes[find_nation(german)] == rothmans
        // the green house's owner drinks coffee
        && drinks[find_colour(green)] == coffee
        // the owner of the yellow house smokes Dunhill
        && cigarettes[find_colour(yellow)] == dunhill
        // the person who smokes Pall Mall rears birds
        && animals[find_cigarette(pallmall)] == bird
        // the person who smokes Winfield drinks beer
        && drinks[find_cigarette(winfield)] == beer
        && norwegian_lives_next_to_blue_house()
        && green_left_of_white()
        && smokes_marlboro_next_cats()
        && smokes_dunhill_next_horses()
        && smokes_marlboro_next_water();
}

// print the details for all houses
void print()
{
    std::cout << '\n';
    for (int i = 0; i < num_houses; ++i)
        std::cout
            << "house " << i + 1
            << ' ' << colour_names[colours[i]]
            << ' ' << nation_names[nations[i]]
            << ' ' << animal_names[animals[i]]
            << ' ' << cigarette_names[cigarettes[i]]
            << ' ' << drink_names[drinks[i]]
            << "\n";
}

}//anonymous namespace

int main ()
{
    init(nations);
    init(colours);
    init(animals);
    init(cigarettes);
    init(drinks);
    if (is_solution())
        print();

    while (permute()) {
        if (is_solution())
            print();
    }
}
