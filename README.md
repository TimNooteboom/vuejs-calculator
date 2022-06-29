#Calculator with Vite
https://www.youtube.com/watch?v=NaZwc8eQ9Xk


npm init vite-app calculator
cd calculator
npm install
npm run dev


********
Fix: divide by zero
Make a calculator component
Make it all use composition api
Make font smaller if length > ?
Get a product then click 'del'
Fix CSS for buttons

Possible improvements:

remember previous and current values as you type the operator
create a data property called operator: null

for +, -, *, /
this.operator = (a,b) => a + b
this.operator = (a,b) => a - b

then when you call equal
this.dispaly = this.operator(previous, current)
