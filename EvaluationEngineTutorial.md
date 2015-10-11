The questioner has two questions that have to be answered

```
/// <summary>
/// Asks questions wihtout knowing how they are answered.
/// </summary>
public class Questioner
{
    private readonly IEvaluationEngine evaluationEngine;

    /// <summary>
    /// Initializes a new instance of the <see cref="Questioner"/> class.
    /// </summary>
    /// <param name="evaluationEngine">The evaluation engine.</param>
    public Questioner(IEvaluationEngine evaluationEngine)
    {
        this.evaluationEngine = evaluationEngine;
    }

    /// <summary>
    /// Asks questions on the evaluation engine without knowledge how they are solved.
    /// </summary>
    public void Ask()
    {
        const string SampleText = "sample Text";

        string coolness = this.evaluationEngine.Answer(new HowCoolIsTheEvaluationEngine());

        int vowels = this.evaluationEngine.Answer(
            new HowManyVowelsAreInThisText { CountLetters = HowManyVowelsAreInThisText.Letters.Small }, 
            SampleText);

        string message = string.Format(
            CultureInfo.InvariantCulture,
            "knowing that {0} has {1} vowels is {2} cool!",
            SampleText,
            vowels,
            coolness);
        Console.WriteLine(message);
    }
}
```

The first question (`HowCoolIsTheEvaluationEngine`) is a parameter-less question, the second (`HowManyVowelsAreInThisText`) a question with a parameter (= the text to process).

The answerer knows how this questions can be solved:

```
/// <summary>
/// Knows how questions can be answered and defines the solution strategy on the evaluation engine.
/// </summary>
public class Answerer
{
    private readonly IEvaluationEngine evaluationEngine;

    /// <summary>
    /// Initializes a new instance of the <see cref="Answerer"/> class.
    /// </summary>
    /// <param name="evaluationEngine">The evaluation engine.</param>
    public Answerer(IEvaluationEngine evaluationEngine)
    {
        this.evaluationEngine = evaluationEngine;
    }

    /// <summary>
    /// Prepares the answers by defining the solution strategy on the evaluation engine.
    /// </summary>
    public void PrepareAnswers()
    {
        this.evaluationEngine.Solve<HowCoolIsTheEvaluationEngine, string>()
            .AggregateWithExpressionAggregator(string.Empty, (aggregate, value) => aggregate + " " + value)
            .ByEvaluating((q, p) => "extremely")
            .ByEvaluating((q, p) => "super")
            .ByEvaluating((q, p) => "fantastic");

        this.evaluationEngine.Solve<HowManyVowelsAreInThisText, int, string>()
            .AggregateWithExpressionAggregator(0, (aggregate, value) => aggregate + value)
            .When(q => q.CountLetters == HowManyVowelsAreInThisText.Letters.Small)
                .ByEvaluating(q => new CountVowelExpression { Vowel = 'a' })
                .ByEvaluating(q => new CountVowelExpression { Vowel = 'e' })
                .ByEvaluating(q => new CountVowelExpression { Vowel = 'i' })
                .ByEvaluating(q => new CountVowelExpression { Vowel = 'o' })
                .ByEvaluating(q => new CountVowelExpression { Vowel = 'u' })
            .When(q => q.CountLetters == HowManyVowelsAreInThisText.Letters.Capital)
                .ByEvaluating(q => new CountVowelExpression { Vowel = 'A' })
                .ByEvaluating(q => new CountVowelExpression { Vowel = 'E' })
                .ByEvaluating(q => new CountVowelExpression { Vowel = 'I' })
                .ByEvaluating(q => new CountVowelExpression { Vowel = 'O' })
                .ByEvaluating(q => new CountVowelExpression { Vowel = 'U' });
    }
}
```

The coolness question is answered by concatenating the results of the individual expressions (here, inline expressions are used).

The vowel counting question is answered depending on whether capital or small letters should be count. The total number is calculated as the sum of the counts of the individual vowels.

The console output of calling Ask on the questioner results in
```
knowing that sample Text has 3 vowels is  extremely super fantastic cool!
```

You can use the Log4NetExtension to log using log4net (or your own logging extension for other logging frameworks).
Answering the coolness question results in the following log:
```
INFO - Question = how cool is the evaluation engine?
Answer =  extremely super fantastic
Used strategy = aggregator strategy
Used Aggregator = expression aggregator with seed '' and aggregate function (aggregate, value) => ((aggregate + " ") + value)
Expressions =
    inline expression = (q, p) => "extremely" => extremely
    inline expression = (q, p) => "super" => super
    inline expression = (q, p) => "fantastic" => fantastic
```

Answering the vowel counting results in the following log:
```
INFO - Question = how many Small vowels are in this text?
Parameter = sample Text
Answer = 3
Used strategy = aggregator strategy
Used Aggregator = expression aggregator with seed '0' and aggregate function (aggregate, value) => (aggregate + value)
Expressions =
    counts the number of a => 1
    counts the number of e => 2
    counts the number of i => 0
    counts the number of o => 0
    counts the number of u => 0
```