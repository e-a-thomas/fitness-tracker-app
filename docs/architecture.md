# Diagram
                  ┌──────────────┐
                  │   SwiftUI    │
                  │     App      │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │ Application  │
                  │    Logic     │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │  SwiftData   │
                  │  Persistence │
                  └──────┬───────┘
                         │
                    App Group
                         │
                 ┌───────┴────────┐
                 ▼                ▼
          ┌────────────┐   ┌────────────┐
          │    App     │   │  WidgetKit │
          └────────────┘   └────────────┘
          

## SwiftUI 
The Apple provided framework for apps and widgets and what I intend to use for this project 

## SwiftData 
allows for me to have local persistence on my Iphone. This data should not be excessively large so local storage is nice, private, and free. 

## App Groups 
Necessary because the main app and the widget are going to be separate targets referencing and interacting with the same data 


# Usage Flow: 
User begins workout
        ↓
Workout is editable while started
        ↓
User ends workout
        ↓
Workout saved
        ↓
SwiftData updated
        ↓
Widget refresh requested
        ↓
Widget queries relevant data
        ↓
7-day gym count updated
