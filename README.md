# tkr
технология командной разработки


Imports System.Math
Imports System.Windows.Markup
Imports System.Threading
Imports SecantMethod.Parse
Imports System.Globalization
Imports SecantMethod.Derivative
Public Class Calculateri
    Dim sw As New Stopwatch
    Public myParse As New Parse
    Public myDer As New Derivative
    Private root As Double
    Private k1 As Integer
    Private abs, endF As Double
    Private fx As String
    Private time As Double

    Public Sub Calc(fx As String, fdr As String, tol As Double, k_max As Integer, x As Double, t As Double)
        sw.Reset()
        sw.Start()
        Dim k_max2, k As Integer
        Dim msg, title As String
        Dim style As MsgBoxStyle
        Dim response As MsgBoxResult
        Dim delta, FX0, FDX, x1, x0 As Double
        Dim error2 As Boolean
        k_max2 = k_max
        x1 = 0
        x0 = x
        k = 0
        Form1.ProgressBar1.Minimum = 0
        Form1.ProgressBar1.Maximum = 100
        error2 = False
        Do

            If (Form1.ProgressBar1.Value >= 100) Then
                Form1.ProgressBar1.Value = 0
            End If
            FX0 = Double.Parse(myParse.parsering(fx, x0)) 'вызывем функцию "parsering" из класса "Parse" чтобы распарсить нашу функцию
            FDX = Double.Parse(myParse.parsering(fdr, x0)) 'вызывем функцию "parsering" из класса "Parse" чтобы распарсить нашу производную от функции

            If (Math.Abs(FDX) > tol) Then
                x1 = x0 - FX0 / FDX 'находим х1 по алгоритму Ньютон метод
            Else

                Exit Do

            End If
            delta = Math.Abs(x1 - x0)

            k = k + 1
            x0 = x1
            Form1.ProgressBar1.Value += 1
            Thread.Sleep(50)

            If (k > k_max) Then

                sw.Stop()
                msg = "Не хватает итерации для точного решения, добавить итерацию?"
                style = MsgBoxStyle.DefaultButton2 Or MsgBoxStyle.Critical Or MsgBoxStyle.YesNo
                title = "Newton Method"
                response = MsgBox(msg, style, title)
                If response = MsgBoxResult.Yes Then
                    k_max += k_max2
                    sw.Start()
                Else
                    k = k - 1
                    Exit Do

                End If
            End If
            If (t <= sw.ElapsedMilliseconds) Then
                sw.Stop()

                msg = "Не хватает времени для точного решения, добавить время?"
                style = MsgBoxStyle.DefaultButton2 Or MsgBoxStyle.Critical Or MsgBoxStyle.YesNo
                title = "Newton Method"
                response = MsgBox(msg, style, title)
                If response = MsgBoxResult.Yes Then
                    t = t + 10000
                    sw.Start()
                Else
                    k = k - 1
                    Exit Do

                End If
            End If

        Loop While (delta > tol * Math.Abs(x1)) 'проверяем abs(x1-x0) > tol по алгоритму Ньютон метод
        sw.Stop()
        If (tol <= 0.0000000000000001 And delta > tol) Then
            If (k_max <= k) Then
                Form1.Label11.Text = "Программа не может вычислить заданной погрешностью" + tol.ToString("0e0", CultureInfo.InvariantCulture) + " " + "из за нехватки кол-во итерации"
            End If
            If (t <= sw.ElapsedMilliseconds) Then
                Form1.Label11.Text = "Программа не может вычислить заданной погрешностью" + tol.ToString("0e0", CultureInfo.InvariantCulture) + " " + "из за нехватки времени"

            End If

        End If
        abs = delta
        k1 = k
        Form1.ProgressBar1.Value = 100
        root = x1
        time = sw.ElapsedMilliseconds / 1000
        endF = myParse.parsering(fx, x1)
    End Sub
    Public Function getResultRoot() As Double
        Return root
    End Function
    Public Function getResultK() As Integer
        Return k1
    End Function
    Public Function getResultTime() As Double
        Return time
    End Function
    Public Function getResultAbs() As Double
        Return abs
    End Function
    Public Function getResultEndF() As Double
        Return endF
    End Function
End Class
