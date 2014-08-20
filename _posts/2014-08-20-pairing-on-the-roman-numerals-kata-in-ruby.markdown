---
layout: post
title:  "Pairing on the Roman Numerals kata in Ruby"
date:   2014-08-20
---

I spend this morning pairing with a student apprentice on the Roman Numerals kata in Ruby. The problem to be solved in this kata is writing code to take an Arabic numeral (e.g., 4) and return the corresponding Roman numeral (IV). This kata, like the others I’m aware of, is supposed to be solved using test-driven development.

The apprentice had spent some time earlier this week coming up with their own solution to the problem. They’d written tests up to the number 45 (skipping the tests for some numbers where they felt it was appropriate):

{% highlight ruby linenos %}
describe RomanNumerals do

  before :each do
    @start_convert = RomanNumerals.new
  end

  it "initializes with a hash." do
    numerals_hash = {1 => "I", 2 => "II", 3 => "III", 4 => "IV", 
                    5 => "V", 6 => "VI", 7 => "VII", 
                    8 => "VIII", 9 => "IX", 10 => "X", 40 => "XL", 
                    50 => "L", 100 => "C",
                    500 => "D", 1000 => "M"}
    expect(@start_convert.numerals_hash).to eq(numerals_hash)
  end

  it "returns equivalent of 10" do
    result = "X"
    expect(@start_convert.convert(10)).to eq(result)
  end

  it "returns equivalent of 1" do
    result = "I" 
    expect(@start_convert.convert(1)).to eq(result)
  end

  it "returns equivalent of 2" do
    result = "II"
    expect(@start_convert.convert(2)).to eq(result)
  end

  it "returns equivalent of 3" do
    result = "III"
    expect(@start_convert.convert(3)).to eq(result)
  end

  it "returns equivalent of 4" do
    result = "IV"
    expect(@start_convert.convert(4)).to eq(result)
  end

  # Specs for 5–8, 12–15, 19, 20, 22–25, 30, 35, 40, 45 removed to save space

end
{% endhighlight %}

And they had ended the previous day with the following code (which was passing all but two of the tests):

{% highlight ruby linenos %}
class RomanNumerals
  attr_reader :numerals_hash

  def initialize
    @numerals_hash = {1 => "I", 2 => "II", 3 => "III", 4 => "IV", 
                      5 => "V", 6 => "VI", 7 => "VII", 
                      8 => "VIII", 9 => "IX", 10 => "X", 
                      40 => "XL", 50 => "L", 100 => "C",
                      500 => "D", 1000 => "M"}
  end

  def convert(number)

    @numerals_hash.each do |arabic, roman|
      equivalent = ""
      difference = number - 10
      last_numeral = difference - 10
      ten_divisible = number - last_numeral

      ten_base = ten_divisible/10 

      if arabic == number
        return roman
      else
        if arabic == difference
            equivalent += "X"
            equivalent += roman
            return equivalent
        elsif arabic == last_numeral
            equivalent += "X" * ten_base 
            equivalent += roman
            return equivalent
        # elsif arabic == ten_divisible
        #     equivalent += roman
        #     equivalent += "V"
        end
      end
    end 
  end
end
{% endhighlight %}

We reviewed this code at the beginning of the session and ultimately agreed to start from scratch. The apprentice’s primary reason for suggesting this seemed to be that they were unsure how to continue solving the problem with the existing code.

While it would have been a useful exercise to refactor this code—changing its structure to increase its expressiveness without changing its behavior—and then continue from that point, my primary reason for agreeing that we should start over was that I had the feeling that the apprentice hadn’t closely enough followed the 3 rules of TDD in writing their code.

For reference, here are those rules, taken from *[The Clean Coder][]*:

  1. You are not allowed to write any production code until you have first written a failing unit test.
  2. You are not allowed to write more of a unit test than is sufficient to fail—and not compiling is failing.
  3. You are not allowed to write more production code than is sufficient to pass the currently failing unit test.

Since the apprentice had been assigned this kata in order to practice test-driven development, I thought it would be a better idea to go back and try to solve the problem with those rules in mind.

## The result of the pair

{% highlight ruby linenos %}
class RomanNumerals
  def convert(number)
    roman = ""
    bases = { 10 => "X", 5 => "V" }

    if number == 4
      roman = "IV"
    elsif number == 9
      roman = "IX"
    elsif number == 14
      roman = "XIV"
    elsif number == 19
      roman = "XIX"
    else
      bases.each do |arabic, base|
        if number >= arabic
          roman += base
          number -= arabic
        end
      end
      roman += ("I" * number)
    end

    roman
  end
end
{% endhighlight %}

{% highlight ruby linenos %}
describe RomanNumerals do
  describe "#convert" do
    before :each do
      @roman_numerals = RomanNumerals.new
    end

    it "Returns I when passed 1" do
      expect(@roman_numerals.convert(1)).to eq("I")
    end

    it "Returns II when passed 2" do
      expect(@roman_numerals.convert(2)).to eq("II")
    end

    it "Returns III when passed 3" do
      expect(@roman_numerals.convert(3)).to eq("III")
    end

    it "Returns IV when passed 4" do
      expect(@roman_numerals.convert(4)).to eq("IV")
    end

    # Specs for 5–15, 19 removed to save space

  end
end
{% endhighlight %}

While the difference in length is deceptive, given that we didn’t have enough time to get to the point the apprentice had gotten to previously, we both felt the new code was an improvement from the original. While we ended with it in a state that still hard-codes some values (i.e., the results for 4, 9, 14, and 19), we agreed that the new code was more expressive. A big factor in the increased readability was the removal of the variables `difference`, `last_numeral`, `ten_divisible`, and `ten_base`, which did not ever become necessary as we test-drove our solution.

The process, however, was more important than the result. Throughout our session, we strictly adhered to the rules of TDD—always writing our tests first, and only writing as much test/code as was needed at every increment. We were also careful to make sure we weren’t thinking beyond the current test. One of the biggest realizations that came out of the pair was that some of the extra complexity in the original code had resulted from the apprentice’s thinking too far ahead, making assumptions about what they would need.

A final note: We might have gotten further than we did had that been our only objective. However, there were a number of points along the way where we took time to discuss certain related-but-not-directly-relevant things (for example, the [SOLID principles][]), and I think we both learned more as a result.

[The Clean Coder]: http://www.amazon.com/The-Clean-Coder-Professional-Programmers/dp/0137081073
[SOLID principles]: http://en.wikipedia.org/wiki/SOLID_(object-oriented_design)
