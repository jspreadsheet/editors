# Jspreadsheet Editors

Jspreadsheet editors are components that assist users in inputting cells during the edition. Jspreadsheet Pro offers native components, from elementary text fields to sophisticated interactive widgets. It also provides an API so developers can create their custom components.


## Documentation

### Custom editors

Customizing cell data entry using custom editors can enhance the user experience and data collection in your data grid. A custom editor is defined as a JS object with several methods described below.

| Method      | Description                                                                                                                                                          |
| ------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| createCell  | When a new cell is created.<br/>`createCell(cell: Object, value: Any, x: Number, y: Number, instance: Object, options: Object) : void`                               |
| updateCell  | When the cell value changes.<br/>`updateCell(cell: Object, value: Any, x: Number, y: Number, instance: Object, options: Object) : void`                              |
| openEditor  | When the user starts editing a cell.<br/>`openEditor(cell: Object, value: Any, x: Number, y: Number, instance: Object, options: Object) : void`                      |
| closeEditor | When the user finalizes the edit of a cell.<br/>`closeEditor(cell: Object, confirmChanges: Boolean, x: Number, y: Number, instance: Object, options: Object) : void` |
| destroyCell | When a cell is destroyed.<br/>`destroyCell(cell: Object, x: Number, y: Number, instance: Object) : void`                                                             |
| get         | Transform the raw data into processed data. It will show text instead of an id in the type dropdown, for example.<br/>`get(options: Object, value: Any) : Any`       |


### Example

```html
<div id="spreadsheet"></div>

<script>
const Editor = {
    updateCell: function(cell, value, x, y, instance) {
        value = Number(value) || 0;
        if (cell) {
            cell.innerText = value + '%';
        }
        return value / 100;
    },
    createCell: function(cell, value, x, y, instance) {
        value = Number(value) || 0;
        if (cell) {
            cell.innerText = value + '%';
        }
        return value / 100;
    },
    openEditor: function(cell, value, x, y, instance) {
        instance.parent.input.onblur = function() {
            instance.closeEditor(cell, true);
        };
        if (value) {
            instance.parent.input.innerText = (Number(value)) * 100;
        }
        jSuites.focus(instance.parent.input);
    },
    closeEditor: function(cell, save, x, y, instance) {
        let value;
        if (save) {
            value = Number(instance.parent.input.innerText);
        } else {
            value = '';
        }

        return value;
    },
    get: function(options, val) {
        return (val * 100) + '%';
    }
}

const data = [
    ['PHP', '1'],
    ['Javascript', '0.4'],
];

// Set your JSS license key (The following key only works for one day)
jspreadsheet.setLicense('###license###');

// Create a new spreadsheet
jspreadsheet(document.getElementById('spreadsheet'), {
    data: data,
    columns: [
        {
            type: 'text',
            title: 'Course Title',
            width: '300px'
        },
        {
            type: Editor,
            title: 'Percent',
            width: '200px'
        },
     ]
});
</script>
```
```jsx
import React, { useRef } from "react";
import { Spreadsheet, Worksheet } from "@jspreadsheet/react";
import { createRoot } from "react-dom/client";
import Switch from "@mui/material/Switch";

const license = '###license###';

const Editor = {
    createCell: (cell, value, x, y, instance, options) => {
        // Clone the column definitions
        let o = { ...options, defaultChecked: !!value };
        // type is a reserved property
        delete o.type;
        // Change handler
        const onChange = function(event, newValue) {
            // Update the JSS data based on the component
            instance.setValue(cell, newValue);
        }
        // Create the link between react component and JSS
        createRoot(cell).render(<Switch {...o} onChange={onChange} />);
    },
    updateCell: (cell, value) => {
        // Get the checkbox
        let input = cell.querySelector('input');
        // Toggle the value
        input.checked = !! value;
    },
    openEditor: (cell, value, x, y, instance) => {
        // Toggle the value
        instance.setValue(cell, !value);
        // Do not open editor since is just a checkbox
        return false;
    },
    closeEditor: () => {
        // This is not used
        return false;
    }
}

export default function App() {
    // Spreadsheet array of worksheets
    const spreadsheet = useRef();
    // Data
    const data = [[true]],
    // Columns
    columns: [
        {
            type: Editor,
            color: "warning",
        }
    ];

    // Render data grid component
    return (
        <Spreadsheet ref={spreadsheet} license={license}>
            <Worksheet data={data} columns={columns} />
        </Spreadsheet>
    );
}
```
