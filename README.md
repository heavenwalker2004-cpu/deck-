import React, { useMemo, useState, useEffect } from "react";
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Badge } from "@/components/ui/badge";
import { Avatar, AvatarFallback, AvatarImage } from "@/components/ui/avatar";
import { Dialog, DialogContent, DialogHeader, DialogTitle, DialogTrigger, DialogDescription } from "@/components/ui/dialog";
import { Sheet, SheetContent, SheetHeader, SheetTitle, SheetTrigger } from "@/components/ui/sheet";
import { Textarea } from "@/components/ui/textarea";
import { Checkbox } from "@/components/ui/checkbox";
import { Switch } from "@/components/ui/switch";
import { Progress } from "@/components/ui/progress";
import { Tooltip, TooltipContent, TooltipProvider, TooltipTrigger } from "@/components/ui/tooltip";
import { Bell, Calendar, CheckCircle2, FileText, LogIn, LogOut, MessageSquare, Search, ShieldCheck, Star, UploadCloud, WalletCards, Users } from "lucide-react";
import { motion } from "framer-motion";

/**
 * Deck of Ace’s — MVP single-file demo
 * - Landing page + Expert search
 * - Client Dashboard (Bookings, Documents, Messages, Payments, Profile)
 * - Professional Dashboard (Leads, Schedule, KYC/Verification, Invoicing)
 * - Role switcher (Client/Professional) stored in localStorage
 * - Mock data + non-persistent state (safe demo)
 *
 * Notes
 * - This file is previewable in ChatGPT canvas.
 * - For production, split into routes/components and wire to a backend (Supabase/Firebase/Nest/Express).
 */

const mockExperts = [
  {
    id: "exp-1",
    name: "Aarav Mehta, CA",
    role: "Chartered Accountant",
    city: "Bengaluru",
    languages: ["English", "Hindi", "Kannada"],
    fee: 1499,
    rating: 4.8,
    reviews: 132,
    verified: true,
    specialties: ["Tax Filing", "GST", "Startup Finance"],
    avatar: "https://images.unsplash.com/photo-1607746882042-944635dfe10e?q=80&w=300&auto=format&fit=crop",
    availability: "This week",
  },
  {
    id: "exp-2",
    name: "Neha Sharma, Advocate",
    role: "Corporate Lawyer",
    city: "Mumbai",
    languages: ["English", "Hindi", "Marathi"],
    fee: 2499,
    rating: 4.6,
    reviews: 98,
    verified: true,
    specialties: ["Contracts", "IPR", "Compliance"],
    avatar: "https://images.unsplash.com/photo-1544005313-94ddf0286df2?q=80&w=300&auto=format&fit=crop",
    availability: "Today",
  },
  {
    id: "exp-3",
    name: "Rohan Iyer, CS",
    role: "Company Secretary",
    city: "Pune",
    languages: ["English", "Hindi", "Marathi"],
    fee: 1299,
    rating: 4.4,
    reviews: 67,
    verified: false,
    specialties: ["ROC Filings", "Board Advisory"],
    avatar: "https://images.unsplash.com/photo-1547425260-76bcadfb4f2c?q=80&w=300&auto=format&fit=crop",
    availability: "Next week",
  },
  {
    id: "exp-4",
    name: "Tanvi Gupta, CFA",
    role: "Financial Advisor",
    city: "Delhi",
    languages: ["English", "Hindi"],
    fee: 1999,
    rating: 4.9,
    reviews: 210,
    verified: true,
    specialties: ["Wealth Planning", "Retirement", "Mutual Funds"],
    avatar: "https://images.unsplash.com/photo-1551836022-d5d88e9218df?q=80&w=300&auto=format&fit=crop",
    availability: "This week",
  },
];

function useLocalStorage(key, initial) {
  const [state, setState] = useState(() => {
    try {
      const raw = localStorage.getItem(key);
      return raw ? JSON.parse(raw) : initial;
    } catch {
      return initial;
    }
  });
  useEffect(() => {
    try { localStorage.setItem(key, JSON.stringify(state)); } catch {}
  }, [key, state]);
  return [state, setState];
}

function Nav({ role, onRoleChange }) {
  return (
    <div className="sticky top-0 z-40 border-b bg-white/70 backdrop-blur">
      <div className="mx-auto flex max-w-7xl items-center justify-between px-4 py-3">
        <div className="flex items-center gap-2">
          <ShieldCheck className="h-6 w-6" />
          <span className="font-semibold">Deck of Ace’s</span>
          <Badge variant="secondary" className="ml-2">Beta</Badge>
        </div>
        <div className="flex items-center gap-3">
          <TooltipProvider>
            <Tooltip>
              <TooltipTrigger asChild>
                <div className="flex items-center gap-2 rounded-full border px-2 py-1">
                  <Users className="h-4 w-4" />
                  <Select value={role} onValueChange={onRoleChange}>
                    <SelectTrigger className="h-7 w-[150px] border-none bg-transparent p-0 focus:ring-0">
                      <SelectValue />
                    </SelectTrigger>
                    <SelectContent>
                      <SelectItem value="client">Client</SelectItem>
                      <SelectItem value="pro">Professional</SelectItem>
                    </SelectContent>
                  </Select>
                </div>
              </TooltipTrigger>
              <TooltipContent>Select your view</TooltipContent>
            </Tooltip>
          </TooltipProvider>
          <Button variant="outline" size="sm"><LogIn className="mr-2 h-4 w-4"/>Sign in</Button>
          <Button size="sm" className="rounded-2xl">Get Started</Button>
        </div>
      </div>
    </div>
  );
}

function Hero() {
  return (
    <div className="relative overflow-hidden">
      <div className="mx-auto grid max-w-7xl grid-cols-1 items-center gap-10 px-4 py-12 md:grid-cols-2">
        <motion.div initial={{opacity:0, y:10}} animate={{opacity:1,y:0}} transition={{duration:0.6}}>
          <h1 className="text-3xl font-bold md:text-5xl">Find verified legal & financial experts in minutes</h1>
          <p className="mt-3 text-muted-foreground">Smart filters, verified profiles, and seamless consultations—built for newcomers to any city.</p>
          <div className="mt-6 flex flex-wrap gap-3">
            <Badge variant="outline" className="text-sm"><ShieldCheck className="mr-1 h-4 w-4"/>Verified</Badge>
            <Badge variant="outline" className="text-sm"><Calendar className="mr-1 h-4 w-4"/>Instant Booking</Badge>
            <Badge variant="outline" className="text-sm"><WalletCards className="mr-1 h-4 w-4"/>Secure Payments</Badge>
          </div>
        </motion.div>
        <motion.div initial={{opacity:0, y:10}} animate={{opacity:1,y:0}} transition={{duration:0.6, delay:0.1}}>
          <Card className="shadow-lg">
            <CardHeader>
              <CardTitle className="text-lg">Quick Search</CardTitle>
            </CardHeader>
            <CardContent>
              <SearchFilters />
            </CardContent>
          </Card>
        </motion.div>
      </div>
    </div>
  );
}

function SearchFilters({ onResults }) {
  const [q, setQ] = useState("");
  const [city, setCity] = useState("");
  const [lang, setLang] = useState("");
  const [maxFee, setMaxFee] = useState("");
  const [availableNow, setAvailableNow] = useState(false);

  const results = useMemo(() => {
    return mockExperts.filter(e => {
      const matchesQ = q ? (e.role.toLowerCase().includes(q.toLowerCase()) || e.specialties.join(" ").toLowerCase().includes(q.toLowerCase())) : true;
      const matchesCity = city ? e.city === city : true;
      const matchesLang = lang ? e.languages.includes(lang) : true;
      const matchesFee = maxFee ? e.fee <= Number(maxFee) : true;
      const matchesAvail = availableNow ? e.availability === "Today" : true;
      return matchesQ && matchesCity && matchesLang && matchesFee && matchesAvail;
    });
  }, [q, city, lang, maxFee, availableNow]);

  useEffect(() => {
    onResults && onResults(results);
  }, [results, onResults]);

  return (
    <div className="space-y-3">
      <div className="flex items-center gap-2">
        <Input value={q} onChange={e=>setQ(e.target.value)} placeholder="e.g., Tax, Contracts, GST, IP, ROC…" />
        <Button variant="secondary"><Search className="mr-2 h-4 w-4"/>Search</Button>
      </div>
      <div className="grid grid-cols-2 gap-3 md:grid-cols-4">
        <Select value={city} onValueChange={setCity}>
          <SelectTrigger><SelectValue placeholder="City"/></SelectTrigger>
          <SelectContent>
            {['Bengaluru','Mumbai','Delhi','Pune'].map(c=> <SelectItem key={c} value={c}>{c}</SelectItem>)}
          </SelectContent>
        </Select>
        <Select value={lang} onValueChange={setLang}>
          <SelectTrigger><SelectValue placeholder="Language"/></SelectTrigger>
          <SelectContent>
            {['English','Hindi','Kannada','Marathi'].map(c=> <SelectItem key={c} value={c}>{c}</SelectItem>)}
          </SelectContent>
        </Select>
        <Input value={maxFee} onChange={e=>setMaxFee(e.target.value)} type="number" placeholder="Max fee (₹)"/>
        <div className="flex items-center justify-between gap-3 rounded-xl border px-3 py-2">
          <div className="text-sm">
            <div className="font-medium">Available today</div>
            <div className="text-xs text-muted-foreground">Only show instant booking</div>
          </div>
          <Switch checked={availableNow} onCheckedChange={setAvailableNow}/>
        </div>
      </div>
      <ResultsList items={results} />
    </div>
  );
}

function ResultsList({ items }) {
  return (
    <div className="grid grid-cols-1 gap-4 md:grid-cols-2 lg:grid-cols-3">
      {items.map(e => <ExpertCard key={e.id} expert={e} />)}
      {items.length === 0 && (
        <Card>
          <CardContent className="p-6 text-center text-sm text-muted-foreground">No experts match your filters. Try widening your search.</CardContent>
        </Card>
      )}
    </div>
  );
}

function ExpertCard({ expert }) {
  const [open, setOpen] = useState(false);
  return (
    <Card className="rounded-2xl">
      <CardContent className="p-4">
        <div className="flex items-center gap-3">
          <Avatar className="h-12 w-12">
            <AvatarImage src={expert.avatar} />
            <AvatarFallback>{expert.name.split(" ").map(p=>p[0]).join("")}</AvatarFallback>
          </Avatar>
          <div className="min-w-0">
            <div className="flex items-center gap-2">
              <div className="truncate font-semibold">{expert.name}</div>
              {expert.verified && <ShieldCheck className="h-4 w-4 text-green-600"/>}
            </div>
            <div className="text-xs text-muted-foreground">{expert.role} • {expert.city} • {expert.availability}</div>
          </div>
        </div>
        <div className="mt-3 flex flex-wrap items-center gap-2">
          {expert.specialties.map(s => <Badge key={s} variant="outline">{s}</Badge>)}
        </div>
        <div className="mt-3 flex items-center justify-between">
          <div className="flex items-center gap-1 text-sm">
            <Star className="h-4 w-4"/>
            <span className="font-medium">{expert.rating}</span>
            <span className="text-muted-foreground">({expert.reviews})</span>
          </div>
          <div className="text-sm">Fee <span className="font-semibold">₹{expert.fee}</span></div>
        </div>
        <div className="mt-3 flex items-center justify-between text-xs text-muted-foreground">
          <div className="truncate">Speaks: {expert.languages.join(", ")}</div>
          <Dialog open={open} onOpenChange={setOpen}>
            <DialogTrigger asChild>
              <Button size="sm">Book</Button>
            </DialogTrigger>
            <DialogContent>
              <DialogHeader>
                <DialogTitle>Book with {expert.name}</DialogTitle>
                <DialogDescription>Pick a slot and share a short brief. Payment is collected after confirmation.</DialogDescription>
              </DialogHeader>
              <div className="grid gap-3">
                <Label>Preferred Date</Label>
                <Input type="date" />
                <Label>Time</Label>
                <Select>
                  <SelectTrigger><SelectValue placeholder="Select a time"/></SelectTrigger>
                  <SelectContent>
                    {["10:00","12:00","15:00","18:00"].map(t=>(<SelectItem key={t} value={t}>{t}</SelectItem>))}
                  </SelectContent>
                </Select>
                <Label>Brief</Label>
                <Textarea placeholder="Tell us about your matter (2–3 lines)"/>
                <Button className="mt-2">Request Booking</Button>
              </div>
            </DialogContent>
          </Dialog>
        </div>
      </CardContent>
    </Card>
  );
}

function KPI({ label, value, sub }) {
  return (
    <Card>
      <CardContent className="p-4">
        <div className="text-xs text-muted-foreground">{label}</div>
        <div className="text-2xl font-semibold">{value}</div>
        {sub && <div className="text-xs text-muted-foreground">{sub}</div>}
      </CardContent>
    </Card>
  );
}

function ClientDashboard() {
  const [notifs, setNotifs] = useState(true);
  return (
    <div className="mx-auto max-w-7xl px-4 py-8">
      <div className="mb-4 flex items-center justify-between">
        <h2 className="text-xl font-semibold">Client Dashboard</h2>
        <div className="flex items-center gap-3">
          <Switch checked={notifs} onCheckedChange={setNotifs}/>
          <Bell className="h-5 w-5"/>
        </div>
      </div>
      <div className="grid grid-cols-2 gap-3 md:grid-cols-4">
        <KPI label="Upcoming" value="2" sub="This week"/>
        <KPI label="Open Docs" value="4" sub="Awaiting review"/>
        <KPI label="Messages" value="3" sub="New replies"/>
        <KPI label="Payments" value="₹4,498" sub="Last 30 days"/>
      </div>
      <Tabs defaultValue="bookings" className="mt-6">
        <TabsList className="flex flex-wrap">
          <TabsTrigger value="bookings"><Calendar className="mr-2 h-4 w-4"/>Bookings</TabsTrigger>
          <TabsTrigger value="docs"><FileText className="mr-2 h-4 w-4"/>Documents</TabsTrigger>
          <TabsTrigger value="messages"><MessageSquare className="mr-2 h-4 w-4"/>Messages</TabsTrigger>
          <TabsTrigger value="payments"><WalletCards className="mr-2 h-4 w-4"/>Payments</TabsTrigger>
          <TabsTrigger value="profile"><ShieldCheck className="mr-2 h-4 w-4"/>Profile</TabsTrigger>
        </TabsList>
        <TabsContent value="bookings"><Bookings /></TabsContent>
        <TabsContent value="docs"><Documents /></TabsContent>
        <TabsContent value="messages"><Messages /></TabsContent>
        <TabsContent value="payments"><Payments /></TabsContent>
        <TabsContent value="profile"><ClientProfile /></TabsContent>
      </Tabs>
    </div>
  );
}

function Bookings() {
  const items = [
    { id: "b1", expert: "Neha Sharma, Advocate", date: "2025-08-12", time: "12:00", status: "Pending" },
    { id: "b2", expert: "Aarav Mehta, CA", date: "2025-08-14", time: "10:00", status: "Confirmed" },
  ];
  return (
    <div className="grid grid-cols-1 gap-3 md:grid-cols-2">
      {items.map(b => (
        <Card key={b.id}>
          <CardContent className="flex items-center justify-between p-4">
            <div>
              <div className="font-medium">{b.expert}</div>
              <div className="text-xs text-muted-foreground">{b.date} • {b.time}</div>
            </div>
            <Badge variant={b.status === 'Confirmed' ? 'default' : 'secondary'}>{b.status}</Badge>
          </CardContent>
        </Card>
      ))}
    </div>
  );
}

function Documents() {
  const [docs, setDocs] = useState([
    { id: "d1", name: "KYC_Passport.pdf", status: "Uploaded" },
    { id: "d2", name: "Income_Statements.zip", status: "Requested" },
  ]);
  const addDoc = () => setDocs(prev => [...prev, { id: crypto.randomUUID(), name: `Attachment_${prev.length+1}.pdf`, status: "Uploaded" }]);
  return (
    <div className="space-y-3">
      <Button onClick={addDoc}><UploadCloud className="mr-2 h-4 w-4"/>Upload Document</Button>
      <div className="grid grid-cols-1 gap-3 md:grid-cols-2">
        {docs.map(d => (
          <Card key={d.id}>
            <CardContent className="flex items-center justify-between p-4">
              <div>
                <div className="font-medium truncate">{d.name}</div>
                <div className="text-xs text-muted-foreground">Status: {d.status}</div>
              </div>
              <CheckCircle2 className="h-5 w-5"/>
            </CardContent>
          </Card>
        ))}
      </div>
    </div>
  );
}

function Messages() {
  const [text, setText] = useState("");
  const [thread, setThread] = useState([
    { from: "You", body: "Hi, I need a review of my rental agreement." },
    { from: "Neha (Advocate)", body: "Sure, share the PDF here and I’ll get back by EOD." },
  ]);
  const send = () => {
    if(!text.trim()) return;
    setThread(prev => [...prev, { from: "You", body: text.trim() }]);
    setText("");
  };
  return (
    <div className="grid gap-3">
      <Card>
        <CardContent className="space-y-2 p-4">
          {thread.map((m,i) => (
            <div key={i} className="rounded-xl bg-muted p-2 text-sm"><span className="font-medium">{m.from}: </span>{m.body}</div>
          ))}
        </CardContent>
      </Card>
      <div className="flex items-center gap-2">
        <Input value={text} onChange={e=>setText(e.target.value)} placeholder="Type a message…"/>
        <Button onClick={send}><MessageSquare className="mr-2 h-4 w-4"/>Send</Button>
      </div>
    </div>
  );
}

function Payments() {
  const items = [
    { id: "p1", to: "Aarav Mehta, CA", amount: 1499, date: "2025-07-28", status: "Paid" },
    { id: "p2", to: "Neha Sharma, Advocate", amount: 2499, date: "2025-08-01", status: "Pending" },
  ];
  const total = items.reduce((s,i)=>s+i.amount,0);
  return (
    <div className="space-y-3">
      <div className="grid grid-cols-1 gap-3 md:grid-cols-2">
        {items.map(p => (
          <Card key={p.id}>
            <CardContent className="flex items-center justify-between p-4">
              <div>
                <div className="font-medium">{p.to}</div>
                <div className="text-xs text-muted-foreground">{p.date}</div>
              </div>
              <div className="text-right">
                <div className="font-semibold">₹{p.amount}</div>
                <Badge variant={p.status === 'Paid' ? 'default' : 'secondary'}>{p.status}</Badge>
              </div>
            </CardContent>
          </Card>
        ))}
      </div>
      <div className="text-sm text-muted-foreground">Total (sample): ₹{total}</div>
    </div>
  );
}

function ClientProfile() {
  return (
    <div className="grid gap-4 md:grid-cols-3">
      <Card className="md:col-span-2">
        <CardHeader><CardTitle>Profile</CardTitle></CardHeader>
        <CardContent className="grid gap-3">
          <div className="grid grid-cols-1 gap-3 md:grid-cols-2">
            <div>
              <Label>Full name</Label>
              <Input defaultValue="Lokesh Kumar"/>
            </div>
            <div>
              <Label>Email</Label>
              <Input defaultValue="lokesh@example.com"/>
            </div>
            <div>
              <Label>City</Label>
              <Input defaultValue="Bengaluru"/>
            </div>
            <div>
              <Label>Preferred language</Label>
              <Select defaultValue="English">
                <SelectTrigger><SelectValue /></SelectTrigger>
                <SelectContent>
                  {['English','Hindi','Kannada','Marathi'].map(x=> <SelectItem key={x} value={x}>{x}</SelectItem>)}
                </SelectContent>
              </Select>
            </div>
          </div>
          <div className="flex items-center gap-2">
            <Checkbox id="gdpr" defaultChecked />
            <Label htmlFor="gdpr" className="text-sm text-muted-foreground">I agree to secure storage and processing of my data.</Label>
          </div>
          <Button className="w-fit">Save changes</Button>
        </CardContent>
      </Card>
      <Card>
        <CardHeader><CardTitle>Verification</CardTitle></CardHeader>
        <CardContent className="space-y-2">
          <div className="text-sm text-muted-foreground">KYC completion</div>
          <Progress value={72}/>
          <Button variant="secondary" className="w-full">Continue KYC</Button>
        </CardContent>
      </Card>
    </div>
  );
}

function ProDashboard() {
  return (
    <div className="mx-auto max-w-7xl px-4 py-8">
      <div className="mb-4 flex items-center justify-between">
        <h2 className="text-xl font-semibold">Professional Dashboard</h2>
        <Sheet>
          <SheetTrigger asChild>
            <Button variant="outline"><FileText className="mr-2 h-4 w-4"/>Post an Article</Button>
          </SheetTrigger>
          <SheetContent side="right" className="w-full sm:max-w-md">
            <SheetHeader>
              <SheetTitle>Knowledge Center Post</SheetTitle>
            </SheetHeader>
            <div className="mt-4 grid gap-3">
              <Input placeholder="Title"/>
              <Textarea placeholder="Write your insights…" rows={10}/>
              <Button>Publish</Button>
            </div>
          </SheetContent>
        </Sheet>
      </div>
      <div className="grid grid-cols-2 gap-3 md:grid-cols-4">
        <KPI label="New Leads" value="5" sub="Past 7 days"/>
        <KPI label="Bookings" value="7" sub="This month"/>
        <KPI label="Avg. Rating" value="4.7" sub="132 reviews"/>
        <KPI label="Earnings" value="₹38,250" sub="This month"/>
      </div>
      <Tabs defaultValue="leads" className="mt-6">
        <TabsList className="flex flex-wrap">
          <TabsTrigger value="leads"><Search className="mr-2 h-4 w-4"/>Leads</TabsTrigger>
          <TabsTrigger value="schedule"><Calendar className="mr-2 h-4 w-4"/>Schedule</TabsTrigger>
          <TabsTrigger value="kyc"><ShieldCheck className="mr-2 h-4 w-4"/>KYC</TabsTrigger>
          <TabsTrigger value="invoices"><WalletCards className="mr-2 h-4 w-4"/>Invoices</TabsTrigger>
          <TabsTrigger value="profile"><Users className="mr-2 h-4 w-4"/>Profile</TabsTrigger>
        </TabsList>
        <TabsContent value="leads"><Leads /></TabsContent>
        <TabsContent value="schedule"><Schedule /></TabsContent>
        <TabsContent value="kyc"><KYC /></TabsContent>
        <TabsContent value="invoices"><Invoices /></TabsContent>
        <TabsContent value="profile"><ProProfile /></TabsContent>
      </Tabs>
    </div>
  );
}

function Leads() {
  const sample = [
    { id: "l1", name: "Anita Rao", need: "Company incorporation & GST", city: "Bengaluru" },
    { id: "l2", name: "Rahul Singh", need: "IP assignment review", city: "Delhi" },
  ];
  return (
    <div className="grid grid-cols-1 gap-3 md:grid-cols-2">
      {sample.map(l => (
        <Card key={l.id}>
          <CardContent className="flex items-center justify-between p-4">
            <div>
              <div className="font-medium">{l.name}</div>
              <div className="text-xs text-muted-foreground">{l.city} • {l.need}</div>
            </div>
            <div className="flex gap-2">
              <Button size="sm" variant="outline">Message</Button>
              <Button size="sm">Send Proposal</Button>
            </div>
          </CardContent>
        </Card>
      ))}
    </div>
  );
}

function Schedule() {
  const slots = [
    { id: "s1", date: "2025-08-12", time: "10:00", client: "Anita Rao" },
    { id: "s2", date: "2025-08-14", time: "16:00", client: "Rahul Singh" },
  ];
  return (
    <div className="grid grid-cols-1 gap-3 md:grid-cols-2">
      {slots.map(s => (
        <Card key={s.id}>
          <CardContent className="flex items-center justify-between p-4">
            <div>
              <div className="font-medium">{s.client}</div>
              <div className="text-xs text-muted-foreground">{s.date} • {s.time}</div>
            </div>
            <Button variant="secondary" size="sm">Reschedule</Button>
          </CardContent>
        </Card>
      ))}
    </div>
  );
}

function KYC() {
  return (
    <div className="grid gap-4 md:grid-cols-3">
      <Card className="md:col-span-2">
        <CardHeader><CardTitle>Professional Verification</CardTitle></CardHeader>
        <CardContent className="space-y-3">
          <div className="grid grid-cols-1 gap-3 md:grid-cols-2">
            <div>
              <Label>License Number</Label>
              <Input placeholder="e.g., Bar Council / ICAI / ICSI"/>
            </div>
            <div>
              <Label>Years of Experience</Label>
              <Input type="number" placeholder="5"/>
            </div>
            <div>
              <Label>Primary City</Label>
              <Input placeholder="Bengaluru"/>
            </div>
            <div>
              <Label>Hourly Rate (₹)</Label>
              <Input type="number" placeholder="2000"/>
            </div>
          </div>
          <Button className="w-fit">Submit for Review</Button>
        </CardContent>
      </Card>
      <Card>
        <CardHeader><CardTitle>Status</CardTitle></CardHeader>
        <CardContent>
          <div className="text-sm text-muted-foreground">Profile review progress</div>
          <Progress value={45} className="mt-2"/>
          <div className="mt-2 text-xs text-muted-foreground">Average time: 24–48 hrs</div>
        </CardContent>
      </Card>
    </div>
  );
}

function Invoices() {
  const rows = [
    { id: "inv-101", client: "Anita Rao", amount: 3500, status: "Due", date: "2025-08-05" },
    { id: "inv-102", client: "Rahul Singh", amount: 2200, status: "Paid", date: "2025-07-30" },
  ];
  return (
    <div className="grid grid-cols-1 gap-3 md:grid-cols-2">
      {rows.map(r => (
        <Card key={r.id}>
          <CardContent className="flex items-center justify-between p-4">
            <div>
              <div className="font-medium">{r.id} • {r.client}</div>
              <div className="text-xs text-muted-foreground">{r.date}</div>
            </div>
            <div className="text-right">
              <div className="font-semibold">₹{r.amount}</div>
              <Badge variant={r.status === 'Paid' ? 'default' : 'secondary'}>{r.status}</Badge>
            </div>
          </CardContent>
        </Card>
      ))}
    </div>
  );
}

function ProProfile() {
  return (
    <div className="grid gap-4 md:grid-cols-3">
      <Card className="md:col-span-2">
        <CardHeader><CardTitle>Public Profile</CardTitle></CardHeader>
        <CardContent className="grid gap-3">
          <div className="grid grid-cols-1 gap-3 md:grid-cols-2">
            <div>
              <Label>Display name</Label>
              <Input defaultValue="Neha Sharma, Advocate"/>
            </div>
            <div>
              <Label>Headline</Label>
              <Input defaultValue="Corporate Lawyer • Contracts • IPR"/>
            </div>
            <div className="md:col-span-2">
              <Label>About</Label>
              <Textarea defaultValue="Corporate counsel with 8+ years in contracts & compliance across startups and MSMEs." rows={5}/>
            </div>
          </div>
          <Button className="w-fit">Update profile</Button>
        </CardContent>
      </Card>
      <Card>
        <CardHeader><CardTitle>Visibility</CardTitle></CardHeader>
        <CardContent className="space-y-2">
          <div className="flex items-center justify-between">
            <div className="text-sm">Show verified badge</div>
            <Switch defaultChecked />
          </div>
          <div className="flex items-center justify-between">
            <div className="text-sm">Enable instant booking</div>
            <Switch />
          </div>
        </CardContent>
      </Card>
    </div>
  );
}

export default function App() {
  const [role, setRole] = useLocalStorage("doa_role", "client");
  return (
    <div className="min-h-screen bg-gradient-to-b from-white to-slate-50">
      <Nav role={role} onRoleChange={setRole} />
      <Hero />
      <div className="mx-auto max-w-7xl px-4">
        <div className="py-8">
          <h2 className="mb-3 text-xl font-semibold">Explore Experts</h2>
          <SearchFilters />
        </div>
      </div>
      {role === "client" ? <ClientDashboard /> : <ProDashboard />}
      <footer className="border-t py-10">
        <div className="mx-auto flex max-w-7xl flex-col items-center justify-between gap-4 px-4 md:flex-row">
          <div className="text-sm text-muted-foreground">© {new Date().getFullYear()} Deck of Ace’s • All rights reserved</div>
          <div className="flex items-center gap-4 text-sm">
            <a href="#" className="hover:underline">Privacy</a>
            <a href="#" className="hover:underline">Terms</a>
            <a href="#" className="hover:underline">Contact</a>
            <a href="#" className="hover:underline">Support</a>
          </div>
        </div>
      </footer>
    </div>
  );
}
