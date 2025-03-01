#### 1. Embedding or referencing
##### ==Data that will be accessed together, should be stored together==

![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250228220404.png)
#### 2. Guidelines
![](https://raw.githubusercontent.com/Synergy-io/Notes.io/main/assets/Pasted%20image%2020250228223028.png)
#### 3. one to one relationship
- mostly modeled as an attribute
- can group related fields in sub-document
- referencing
- can model as child references parent. better if we primarily read child
- bi-directional references
#### 4. one to many relationship
- can model as many side doesn't exist without one side
- no duplication

- embed the many side as array of sub-documents in one side
- create one sub-document within parent document 

- array of references within parent document
- reference the parent in each of many child documents
- bi-directional references

- array of sub-documents preferred
	- can retrieve information without $lookup
	- have to consider cardinally
#### 5. many to many
- embedding - array or sub document
	- data duplication happens
	- but not all duplication are bad!

- referencing - array in parent side
- back reference array
- bi-directional reference - not so good, expensive to maintain
