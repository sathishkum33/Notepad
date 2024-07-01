function Decode($sIn) {
    $ABFrom = ""
    $Decode = ""

    # Build ABFrom string
    for ($x = 0; $x -le 25; $x++) {
        $ABFrom += [char](65 + $x)  # A-Z
    }
    for ($x = 0; $x -le 25; $x++) {
        $ABFrom += [char](97 + $x)  # a-z
    }
    for ($x = 0; $x -le 9; $x++) {
        $ABFrom += [string]$x       # 0-9
    }

    # Create abto string by shifting 13 positions to the left
    $abto = $ABFrom.Substring(13) + $ABFrom.Substring(0, 13)

    # Decode the input string
    for ($x = 0; $x -lt $sIn.Length; $x++) {
        $char = $sIn[$x]
        $y = $abto.IndexOf($char)
        if ($y -eq -1) {
            $Decode += $char  # If character is not in abto, leave it unchanged
        } else {
            $Decode += $ABFrom[$y]  # Substitute with the character from ABFrom
        }
    }

    return $Decode
}

# Example usage
$sIn = "Uryyb456"
$decodedString = Decode $sIn
Write-Output $decodedString
