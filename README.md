# JYS FSW

JYS stands for Just Your System. Pronunciation is Juice.

## purpose

- provide all possibility.
- select only needed.
- final product contains minimum.

## approach

- top-down divides with MECE.
- bottom-up prepares from known.
- completion is where they meet.

## rule

- rule should reduce friction in collaboration.
- rule should define term.
- rule should be followable without full read.
- rule should validate itself.
- rule should have clear scope and end.
- this rule itself tries to be so.

## directory rule

1. base directory has a README.md. nested base can exist.
2. depth n directory is child of depth n-1. base is depth 0.
3. depth n directory has a md file: {depth_n}_{depth_n-1}_{...}_{depth_1}.md
4. local README.md can override rules.
5. use snake_case.
6. use singular.
7. files in same base directory form a closed system: mutually consistent.
8. appendix is outside closed system.

## depth 1 directories and files

- README.md, style/: JYS way. how to make.
- fsw/: FSW. what to make.
