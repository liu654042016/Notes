# stack
## 特性
先进后出，后进先出
### 232
```cpp
class MyQueue{
    stack<int> in, out;
public:
    MyQueue() {}

    void push(int x){
        in.push(x);
    }

    int pop(){
        in2out();
        int x = out.top();
        out.pop();
        return x;
    }

    int peek(){
        in2out();
        return out.top();
    }

    void in2out(){
        if(out.empty()){
            while(!in.empty()){
                int x = in.top();
                in.pop();
                out.push(x);
            }
        }
    }
    bool empty(){
        return in.empty() && out.empty();
    }
};

// 155 Min Stack
class MinStack{
    stack<int> s, min_s;
public:
    MinStack(){}
    void push(int x){
        s.push(x);
        if(min_s.empty() || min_s.top() >= x){
            min_s.push(x);
        }
    }
    void pop(){
        if(!min_s.empty() || min_s.top() == s.top()){
            min_s.pop();
        }
        s.pop();
    }
    int top(){
        return s.top();
    }
    int getMin(){
        return min_s.top();
    }
}

//20 valid parentheses
bool isValid(string s){
    stack<char> parsed;
    for(int i=0; i<s.length(); ++i){
        if(s[i] == '{' || s[i] == '[' || s[i] == '('){
            parsed.push(s[i]);
        }else{
            if(parsed.empty()){
                return false;
            }
            char c = parsed.top();
            if(s[i]=='}' && c=='{' ||
               s[i] == ']' && c=='['||
               s[i] == ')' && c=='('){
                parsed.pop();
               }else{
                return false;
               }
        }
    }
    return parsed.empty();
}
```
### 11.4
```cpp
//739. Daily Temperatures
vector<int> dailyTemperatures(vector<int>& temperatures){
    int n = tempeartures.size();
    vector<int> ans(n);
    stack<int> indices;
    for(int i=0; i<n; ++i){
        while(!indices.empty()){
            int pre_index = indices.top();
            
            if(temperatures[i] <= temperatures[pre_index]){
                break;
            }
            indices.pop();
            ans[pre_index] = i - pre_index;
        }
        indices.push(i);
    }
    return ans;
}

```
