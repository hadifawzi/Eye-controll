# Eye-controll

CvCapture cap = CvCapture.FromCamera(1);


privatevoid CaptureCamera()
{
_cameraThread = newThread(newThreadStart(CaptureCameraCallback));
_cameraThread.Start();
}


CvHaarClassifierCascade cascade =
CvHaarClassifierCascade.FromFile("haarcascade_eye.xml");
CvHaarClassifierCascade _faces =
CvHaarClassifierCascade.FromFile("haarcascade_frontalface_alt2.xml");


IplImage img = cap.QueryFrame();
CvSeq<CvAvgComp> eyes = Cv.HaarDetectObjects(img, cascade,
Cv.CreateMemStorage(), ScaleFactor, MinNeighbors,
HaarDetectionType.DoCannyPruning, MinSize);
CvSeq<CvAvgComp> faces = Cv.HaarDetectObjects(img, _faces,
Cv.CreateMemStorage(), 1.1, 1, HaarDetectionType.FindBiggestObject, newCvSize(20,
20));

CvRect faceRect = newCvRect();
foreach (CvAvgComp face in faces.AsParallel())
{
img.DrawRect(face.Rect, CvColor.Blue);
faceRect = face.Rect;
}
int widthNav = (img.Width / 10 * 2);
int heightNav = (img.Height / 10 * 2);
CvRect nav = newCvRect(newCvPoint(img.Width / 2 - widthNav / 2, img.Height / 2 -
heightNav / 2), newCvSize(widthNav, heightNav));
CvRect boundingRect = newCvRect(newCvPoint(pctCvWindow.Width / 5,
pctCvWindow.Height / 5), newCvSize((img.Width - img.Width / 2),
pctCvWindow.Height - pctCvWindow.Height / 5));


foreach (CvAvgComp eye in eyes.AsParallel())
{
if (eye.Rect.Width < pctCvWindow.Width / 7)
{
img.DrawMarker(faceRect.Left + faceRect.Width / 2, faceRect.Top +
faceRect.Height / 2, CvColor.Green);
if (eye.Rect.Left > faceRect.Left+faceRect.Width / 2)
{
try
{
IplImage rightEyeImg1 = img.Clone();
Cv.SetImageROI(rightEyeImg1, eye.Rect);
IplImage rightEyeImg2 = Cv.CreateImage(eye.Rect.Size, rightEyeImg1.Depth,
rightEyeImg1.NChannels);
Cv.Copy(rightEyeImg1, rightEyeImg2, null);
Cv.ResetImageROI(rightEyeImg1);
img.DrawRect(eye.Rect, CvColor.Red);
Bitmap rightEyeBm = BitmapConverter.ToBitmap(rightEyeImg2);
pctRightEye.Image = rightEyeBm;
righteye = true;
}
catch { }
}
else
{
try
{
IplImage leftEyeImg1 = img.Clone();
Cv.SetImageROI(leftEyeImg1, eye.Rect);
IplImage leftEyeImg2 = Cv.CreateImage(eye.Rect.Size, leftEyeImg1.Depth,
leftEyeImg1.NChannels);
Cv.Copy(leftEyeImg1, leftEyeImg2, null);
Cv.ResetImageROI(leftEyeImg1);
img.DrawRect(eye.Rect, CvColor.Yellow);
Bitmap leftEyeBm = BitmapConverter.ToBitmap(leftEyeImg2);
pctLeftEye.Image = leftEyeBm;
lefteye = true;
}
catch { }
          }

if (!righteye)
{
counter++;
}
if (counter == 8)
{
DoMouseClick();
}
if (righteye)
{
counter = 0;
}
if (!lefteye)
{
counterR++;
}
if (counterR ==8)
{
DoMouseClickR();
}
if (lefteye) { counterR = 0 }

if (lefteye && righteye)
{
int xCoord = (this.Width * (corcer.X - nav.Left)) / nav.Width;
int yCoord = (this.Height * (corcer.Y - nav.Top)) / nav.Height;
//We set our( new cursor position
if (Math.Abs(corcer.X - oldX) > img.Width / 1000 &&Math.Abs(corcer.Y - oldY) >
img.Height / 1000)
{
Cursor.Position = newPoint(xCoord, yCoord);
}
oldX = corcer.X;
oldY = corcer.Y;
}
Bitmap bm = BitmapConverter.ToBitmap(img);
bm.SetResolution(pctCvWindow.Width, pctCvWindow.Height);
pctCvWindow.Image = bm;
img = null;
bm = null;
}

if (Math.Abs(corcer.X - oldX) > img.Width / 1000 &&Math.Abs(corcer.Y - oldY) >
img.Height / 1000)
{
Cursor.Position = newPoint(xCoord, yCoord); }


[DllImport("user32.dll")]
staticexternvoid mouse_event(uint dwFlags, uint dx, uint dy, uint dwData,
int dwExtraInfo);
privateconstint MOUSEEVENTF_LEFTDOWN = 0x02;
privateconstint MOUSEEVENTF_LEFTUP = 0x04;
privateconstint MOUSEEVENTF_RIGHTDOWN = 0x08;
privateconstint MOUSEEVENTF_RIGHTUP = 0x10;


publicstaticvoid DoMouseClick()
{
uint X = Convert.ToUInt32(Cursor.Position.X);
uint Y = Convert.ToUInt32(Cursor.Position.Y);
mouse_event(MOUSEEVENTF_LEFTDOWN | MOUSEEVENTF_LEFTUP,
X, Y, 0, 0);
}


ProcessStartInfo startInfo = newProcessStartInfo();
startInfo.FileName = "WINWORD.EXE";
startInfo.Arguments = "";
Process.Start(startInfo);


System.Diagnostics.Process.Start("Temp\\osk.exe");


System.Diagnostics.Process.Start("firefox.exe", "http://www.google.com");
