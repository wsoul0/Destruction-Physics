local module = {}
module.VoxelSize = 2

function module:_clonePart(template): BasePart
	local part = template:Clone()
	part.Parent = template.Parent
	
	return part
end

function module:_getPartsInBounds(cframe: CFrame, size: Vector3 | number, params: OverlapParams?): {BasePart}
	if tonumber(size)  then
		return workspace:GetPartBoundsInRadius(cframe.Position, size, params)
	else
		return workspace:GetPartBoundsInBox(cframe, size, params)
	end
end

function module:DividePart(part: BasePart, axis: Vector3)
	local a = module:_clonePart(part)
	local b = module:_clonePart(part)

	a.Size = part.Size * (-(axis/2)+Vector3.new(1,1,1))
	a.CFrame = part.CFrame * CFrame.new(-part.Size * (Vector3.new(1,1,1)*axis/4))	

	b.Size = part.Size * (-(axis/2)+Vector3.new(1,1,1))
	b.CFrame = part.CFrame * CFrame.new(part.Size * (Vector3.new(1,1,1)*axis/4))
end

function module:SubdividePart(part: BasePart, size: number?): boolean
	
	local randomX = math.random(5,8)
	local randomY = math.random(5,8)
	local randomZ = math.random(5,8)
	
	size = size or module.VoxelSize
	if part.Size.X / randomX < size and part.Size.Y / randomY < size and part.Size.Z / randomZ < size then return false end

	local axis = math.max(part.Size.X, part.Size.Y, part.Size.Z)
	if axis == part.Size.X then
		module:DividePart(part, Vector3.new(1,0,0))
	elseif axis == part.Size.Y then
		module:DividePart(part, Vector3.new(0,1,0))
	else
		module:DividePart(part, Vector3.new(0,0,1))
	end
	
	part:Destroy()
	return true
end

function module:PartDividable(part: BasePart)
	if not part:IsA("Part") then return end
	if part.Shape ~= Enum.PartType.Block then return end
	if part.Parent:FindFirstChild("Humanoid") then return end
	if part.Parent:IsA("Accessory") then return end
	if part:HasTag("NonDestructible") then return end

	return true
end

function module:DestroyPartsInBounds(cframe: CFrame, size: Vector3 | number, params: OverlapParams?, voxelSize: number?, callback: (BasePart) -> ()?)
	local parts
	repeat
		parts = module:_getPartsInBounds(cframe, size, params)
		if #parts == 0 then break end
		for i = 1, #parts do
			if module:PartDividable(parts[i]) then
				local divided = module:SubdividePart(parts[i], voxelSize)
				if not divided then 
					if callback then
						callback(parts[i])
						parts[i] = nil
					else
						parts[i]:Destroy()
					end
				end
			else
				parts[i] = nil
			end
		end
	until #parts == 0
end

return module
