<!--bl
(filemeta
    (title "ABC: Always Be Checking"))
/bl-->

This is not a book about testing or writing tests, so we won't spend a lot of time on tests.  Refactoring, however, has become a common part of the agile software development experience.  As we work through the book, examples and recipes will assume there are tests covering the expected behavior.

When code is written without tests, it does not mean the code will not work, nor does it mean the code will not do the work it is expected to do.  Instead, it means changing the code will be a riskier venture.  Even when using automated refactoring tools, it is not outside the realm of possibility the tool might have a bug or make a mistake.  Having some automated way to verify everything works as expected mitigates the risk and anxiety developers can feel around this kind of potential fragility.

Wherever possible, example code has actually been tested and any microrefactorings have been tested for stability and low risk exposure.  This means, for each action you take in your own source code, be sure to run your tests often and don't skip steps.  The more context a programmer tries to keep in their head, the harder it will be to ensure nothing was overlooked.