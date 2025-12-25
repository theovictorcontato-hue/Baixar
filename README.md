local U={},P=game:GetService("Players").LocalPlayer,PG=P:WaitForChild("PlayerGui"),HS=game:GetService("HttpService")
local F="UILibSave_"..P.UserId
local S={}
pcall(function()S=HS:JSONDecode(readfile(F))end)

local function c(a,b)local o=Instance.new(a)for i,v in pairs(b)do o[i]=v end return o end
local function save()pcall(function()writefile(F,HS:JSONEncode(S))end)end

function U:CreateWindow(t)
local g=c("ScreenGui",{Parent=PG,ResetOnSpawn=false})
local m=c("Frame",{Parent=g,Size=UDim2.fromScale(.35,.45),Position=UDim2.fromScale(.325,.275),BackgroundColor3=Color3.fromRGB(20,20,20),BorderSizePixel=0})
c("UICorner",{Parent=m,CornerRadius=UDim.new(0,10)})
c("TextLabel",{Parent=m,Size=UDim2.fromScale(1,.12),BackgroundTransparency=1,Text=t or "UI",TextColor3=Color3.new(1,1,1),Font=Enum.Font.GothamBold,TextScaled=true})

local tabs=c("Frame",{Parent=m,Size=UDim2.fromScale(.3,.88),Position=UDim2.fromScale(0,.12),BackgroundColor3=Color3.fromRGB(30,30,30),BorderSizePixel=0})
local cont=c("Frame",{Parent=m,Size=UDim2.fromScale(.7,.88),Position=UDim2.fromScale(.3,.12),BackgroundColor3=Color3.fromRGB(25,25,25),BorderSizePixel=0})
local l=Instance.new("UIListLayout",tabs)l.Padding=UDim.new(0,6)

local w={}
function w:CreateTab(n)
local b=c("TextButton",{Parent=tabs,Size=UDim2.new(1,-6,0,40),Text=n,BackgroundColor3=Color3.fromRGB(50,50,50),TextColor3=Color3.new(1,1,1),Font=Enum.Font.Gotham})
c("UICorner",{Parent=b})

local p=c("ScrollingFrame",{Parent=cont,Size=UDim2.fromScale(1,1),CanvasSize=UDim2.new(),ScrollBarImageTransparency=1,Visible=false})
local pl=Instance.new("UIListLayout",p)pl.Padding=UDim.new(0,8)

b.MouseButton1Click:Connect(function()
for _,v in pairs(cont:GetChildren())do if v:IsA("ScrollingFrame")then v.Visible=false end end
p.Visible=true
end)

local t={}
function t:AddButton(k,cb)
local bt=c("TextButton",{Parent=p,Size=UDim2.new(1,-10,0,40),Text=k,BackgroundColor3=Color3.fromRGB(70,70,70),TextColor3=Color3.new(1,1,1),Font=Enum.Font.Gotham})
c("UICorner",{Parent=bt})
bt.MouseButton1Click:Connect(cb)
end

function t:AddToggle(k,cb)
local v=S[k] or false
local bt=c("TextButton",{Parent=p,Size=UDim2.new(1,-10,0,40),Text=k..(v and " ON" or " OFF"),BackgroundColor3=Color3.fromRGB(70,70,70),TextColor3=Color3.new(1,1,1),Font=Enum.Font.Gotham})
c("UICorner",{Parent=bt})
cb(v)
bt.MouseButton1Click:Connect(function()
v=not v
S[k]=v
save()
bt.Text=k..(v and " ON" or " OFF")
cb(v)
end)
end

function t:AddSlider(k,min,max,cb)
local v=S[k] or min
local lb=c("TextLabel",{Parent=p,Size=UDim2.new(1,-10,0,30),Text=k..": "..v,BackgroundTransparency=1,TextColor3=Color3.new(1,1,1),Font=Enum.Font.Gotham})
local sl=c("TextButton",{Parent=p,Size=UDim2.new(1,-10,0,20),BackgroundColor3=Color3.fromRGB(90,90,90),Text=""})
cb(v)
sl.MouseButton1Click:Connect(function()
v=math.clamp(v+1,min,max)
S[k]=v
save()
lb.Text=k..": "..v
cb(v)
end)
end

pl:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
p.CanvasSize=UDim2.new(0,0,0,pl.AbsoluteContentSize.Y+10)
end)

return t
end
return w
end
return U
