function Encode($sIn) {
    $ABFrom = ""
    $Encode = ""

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

    # Encode the input string
    for ($x = 0; $x -lt $sIn.Length; $x++) {
        $char = $sIn[$x]
        $y = $ABFrom.IndexOf($char)
        if ($y -eq -1) {
            $Encode += $char  # If character is not in ABFrom, leave it unchanged
        } else {
            $Encode += $abto[$y]  # Substitute with the character from abto
        }
    }

    return $Encode
}

# Example usage
$sIn = "Hello123"
$encodedString = Encode $sIn
Write-Output $encodedString
