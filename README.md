![version](https://img.shields.io/badge/version-20%20R3%2B-E23089)
# Enable Data Change Tracking
presentation material for 4D Method User Group Meeting

 # Structure

<img src="https://github.com/user-attachments/assets/0e9f6cdf-28e3-494f-952b-06e1830df8bf" width=800 height=auto />

# Phase 1

1. open structure file with 4D 20
2. check products, check clients
3. in structure editor, check contextual menu; no "enable data change tracking"
4. reopen with 20 R3+
5. in structure editor, check contextual menu; new item "enable data change tracking"
6. enable DCT for all 4 tables

<img src="https://github.com/user-attachments/assets/71a7d2cc-f399-4792-8a9d-afe3f10a6fdf" width=260 height=auto />

# Phase 2

1. export to project
2. rename *.4DProject*
3. remove "(binary mode)" in *.4DCatalog*
4. reopen with 20 R3+
5. check settings > web > access > "activate REST authentication through ds.authentify() function"

<img src="https://github.com/user-attachments/assets/3b77eca6-ce16-45cd-8943-d867b76bd637" width=800 height=auto />



