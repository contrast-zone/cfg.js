    /*
        v-parse-cfg algorithm pseudocode
    */

    FUNCTION Parse (grammar, start, inputString)
        DECLARE chart := [][];
        
        MergeItem (0, [start, END_OF_FILE], 0, {Sequence: [], Index: -1, Inherited: [], Inheritors: [], Parents: [], Previous: []}, NULL);
        FOR each new column in chart DO
            FOR each new item in column DO
                FOR each production in grammar WHERE item.Sequence[item.Index] == production.NAME DO
                    MergeItem (column.Index, production.BODY, 0, item, NULL);

        RETURN MakeSyntaxTree ();

        PROCEDURE MergeItem (offset, sequence, index, parent, previous)
            DECLARE item := chart[offset].FIND (sequence, index);

            IF not found item THEN
                item := {Sequence: sequence, Index: index, Inherited: [], Inheritors: [], Parents: [], Previous: []};
                chart[offset].ADD (item);

            IF previous is not NULL and previous not in item.Previous THEN
                item.Previous.ADD (previous);

            IF parent not in item.Parents THEN
                item.Parents.ADD (parent);
                FOR each x in [parent] UNION parent.Inherited DO
                    FOR each y in [item] UNION item.Inheritors DO
                        IF y.Index + 1 == y.Sequence.LENGTH THEN
                            IF (x.Sequence, x.Index) not in y.Inherited THEN
                                y.Inherited.ADD (x);
                                x.Inheritors.ADD (y);

                            IF x.Index + 1 < x.Sequence.LENGTH THEN
                                IF y.Sequence[y.Index] is terminal THEN
                                    IF y.Sequence[y.Index] is at inputString[offset] THEN
                                        FOR each z in x.Parents DO
                                            MergeItem (offset + y.Sequence[y.Index].LENGTH, x.Sequence, x.Index + 1, x.Parents[z], item);

                IF item.index + 1 < item.sequence.length THEN
                    IF item.Sequence[item.Index] is terminal THEN
                        IF item.Sequence[item.Index] is at inputString[offset] THEN
                            MergeItem (offset + item.Sequence[item.Index].LENGTH, item.sequence, item.index + 1, parent, item);

        FUNCTION MakeSyntaxTree ()
            DECLARE item, reachParent, parents, treeItem, childTreeItem;
            
            item := chart[inputString.LENGTH].FIND (END_OF_FILE);
            IF not found item THEN
                RETURN "Error at: " + chart.LENGTH;
            
            parents := [{Sequence: [start, END_OF_FILE], Index: 1, Children: []}];
            WHILE parents.LENGTH > 0;
                IF item.Index > 0 THEN
                    reachParent := {sequence: item.sequence, index: item.index - 1};
                    FOR each p in item.Previous DO
                        IF reachParent is direct or indirect parent of p THEN
                            item := p;
                            EXIT FOR;

                    childTreeItem := inputString.substring (item.offset, item[item.index].LENGTH);
                    parents.LAST.Index := parents.LAST.Index - 1;

                ELSE
                    IF item.Sequence == reachParent.Sequence and item.Index == reachParent.Index THEN
                        reachParent := {sequence: parents.LAST.sequence, index: parents.LAST.index};

                    FOR each p in item.Parents DO
                        IF reachParent is direct or indirect parent of p THEN
                            item := p;
                            EXIT FOR;
                            
                    childTreeItem := treeItem;
                
                IF item.Index == item.Sequence.LENGTH - 1 THEN
                    parents.ADD ({Sequence: item.Sequence, Index: item.Index, Children: []});
                
                treeItem := parents.LAST;
                treeItem.Children[childTreeItem.Index] := childTreeItem;
                
                IF treeItem.Index == 0 THEN
                    parents.REMOVE_LAST ();                
            
            RETURN treeItem;
