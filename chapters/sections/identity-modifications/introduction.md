<!--bl
(filemeta
    (title "Introduction"))
/bl-->

Identity modifications are, in and of themselves, not a refactoring in the sense of making a local modification which immediately leads to clarity.  Instead, an identity modification is a mechanism for introducing some new intended construct or domain idea without actually making any structural changes to the code currently used in production.

Identity modifications are the safest of any microrefactoring actions which could be made. Since an identity modification is unexecuted code, as long as there is no syntax error in the code which leads to something which cannot be interpreted or compiled, the code will run exactly as it did before.